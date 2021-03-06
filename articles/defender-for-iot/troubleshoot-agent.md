---
title: 보안 에이전트 시작 문제 해결 (Linux)
description: Linux 용 IoT 보안 에이전트에 대 한 Azure Defender 작업 문제 해결.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 9c9c36b822ab6acb9f9a48d4ba809ad32f6f4695
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782591"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>보안 에이전트 문제 해결 가이드(Linux)

이 문서에서는 보안 에이전트 시작 프로세스에서 발생할 수 있는 문제를 해결 하는 방법을 설명 합니다.

IoT 용 Azure Defender 에이전트 자체는 설치 후 바로 시작 됩니다. 에이전트 시작 프로세스에는 로컬 구성을 읽고, Azure IoT Hub에 연결 하 고, 원격 쌍 구성을 검색 하는 작업이 포함 됩니다. 이러한 단계 중 하나라도 실패 하면 보안 에이전트가 실패할 수 있습니다.

이 문제 해결 가이드에서는 다음 방법에 대해 알아봅니다.

- 보안 에이전트가 실행 중인지 확인 합니다.
- 보안 에이전트 오류 가져오기
- 보안 에이전트 오류 이해 및 수정

## <a name="validate-if-the-security-agent-is-running"></a>보안 에이전트가 실행 중인지 확인 합니다.

1. 보안 에이전트가 실행 되 고 있는지 확인 하려면 에이전트를 설치한 후 몇 분 정도 기다린 후 다음 명령을 실행 합니다.
     <br>

    **C 에이전트**

    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```

    **C # 에이전트**

    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```

1. 명령에서 빈 줄을 반환 하는 경우 보안 에이전트를 성공적으로 시작할 수 없습니다.

## <a name="force-stop-the-security-agent"></a>보안 에이전트 강제 중지

보안 에이전트를 시작할 수 없는 경우 다음 명령을 사용 하 여 에이전트를 중지 하 고 아래 오류 표를 계속 합니다.

```bash
systemctl stop ASCIoTAgent.service
```

## <a name="get-security-agent-errors"></a>보안 에이전트 오류 가져오기

1. 다음 명령을 실행 하 여 보안 에이전트 오류를 검색 합니다.

    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```

1. 보안 에이전트 오류 가져오기 명령은 Defender for IoT 에이전트에 의해 생성 된 모든 로그를 검색 합니다. 다음 표를 사용 하 여 오류를 이해 하 고 올바른 재구성 단계를 수행 하십시오.

> [!Note]
> 오류 로그는 시간순으로 표시 됩니다. 각 오류의 타임 스탬프를 확인 하 여 수정에 도움을 줍니다.

## <a name="restart-the-agent"></a>에이전트 다시 시작

1. 보안 에이전트 오류를 찾아 수정한 후 다음 명령을 실행 하 여 에이전트를 다시 시작 하십시오.

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. 이전 프로세스를 반복 하 여 중지를 검색 하 고 에이전트에서 시작 프로세스를 계속 실패 하는 경우 오류를 검색 합니다.

## <a name="understand-security-agent-errors"></a>보안 에이전트 오류 이해

대부분의 보안 에이전트 오류는 다음과 같은 형식으로 표시 됩니다.

```
Defender for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```

| 오류 코드 | 오류 하위 코드 | 오류 세부 정보 | 재구성 C | 재구성 C # |
|--|--|--|--|--|
| 로컬 구성 | 누락 된 구성 | 로컬 구성 파일에 구성이 없습니다. 오류 메시지에는 누락 된 키가 명시 되어야 합니다. | 파일의/var/LocalConfiguration.js에 누락 된 키를 추가 합니다. 자세한 내용은 [cs-localconfig-reference](azure-iot-security-local-configuration-c.md) 를 참조 하세요. | General.config 파일에 누락 된 키를 추가 하는 방법에 대 한 자세한 내용은 [c #-localconfig-reference](azure-iot-security-local-configuration-csharp.md) 를 참조 하세요. |
| 로컬 구성 | 구성 구문 분석 하지 못함 | 구성 값을 구문 분석할 수 없습니다. 오류 메시지에는 구문 분석할 수 없는 키가 명시 되어야 합니다. 값이 필요한 형식에 없거나 값이 범위를 벗어났습니다. 구성 값을 구문 분석할 수 없습니다. | LocalConfiguration 스키마와 일치 하도록 파일의/var/LocalConfiguration.js에서 키 값을 수정 합니다. 자세한 내용은 [c #-localconfiguration-reference](azure-iot-security-local-configuration-csharp.md) 를 참조 하세요. | 스키마와 일치 하도록 General.config 파일의 키 값을 수정 하는 방법에 대 한 자세한 내용은 [cs-localconfig-reference](azure-iot-security-local-configuration-c.md) 를 참조 하세요. |
| 로컬 구성 | 파일 형식 | 구성 파일을 구문 분석 하지 못했습니다. | 구성 파일이 손상 되었습니다. 에이전트를 다운로드 한 후 다시 설치 하십시오. | - |
| 원격 구성 | 제한 시간 | 에이전트가 제한 시간 내에 azureiotsecurity 모듈 쌍을 인출할 수 없습니다. | 인증 구성이 올바른지 확인 하 고 다시 시도 하세요. | 에이전트가 제한 시간 내에 azureiotsecurity 모듈 쌍을 인출할 수 없습니다. 인증 구성이 올바른지 확인 하 고 다시 시도 하세요. |
| 인증 | 파일이 없습니다. | 지정 된 경로의 파일이 존재 하지 않습니다. | 파일이 지정 된 경로에 있는지 확인 하거나 파일 **의LocalConfiguration.js** 로 이동 하 여 **FilePath** 구성을 변경 하십시오. | 파일이 지정 된 경로에 있는지 확인 하거나 **Authentication.config** 파일로 이동 하 여 **filePath** 구성을 변경 하십시오. |
| 인증 | 파일 사용 권한 | 에이전트에 파일을 열 수 있는 권한이 없습니다. | **Asciotagent** 사용자에 게 지정 된 경로에 있는 파일에 대 한 읽기 권한을 부여 합니다. | 파일에 액세스할 수 있는지 확인 합니다. |
| 인증 | 파일 형식 | 지정 된 파일의 형식이 올바르지 않습니다. | 파일이 올바른 형식 인지 확인 합니다. 지원 되는 파일 형식은 .pfx 및 pem입니다. | 파일이 올바른 인증서 파일 인지 확인 하십시오. |
| 인증 | 권한 없음 | 에이전트가 지정 된 자격 증명을 사용 하 여 IoT Hub에 대해 인증할 수 없습니다. | LocalConfiguration 파일에서 인증 구성의 유효성을 검사 하 고, 인증 구성을 진행 하 고, 모든 세부 정보가 올바른지 확인 하 고, 파일의 비밀이 인증 된 id와 일치 하는지 확인 합니다. | Authentication.config에서 인증 구성의 유효성을 검사 하 고, 인증 구성을 진행 하 고, 모든 세부 정보가 올바른지 확인 한 후 파일의 비밀이 인증 된 id와 일치 하는지 확인 합니다. |
| 인증 | 찾을 수 없음 | 장치/모듈을 찾았습니다. | 인증 구성 유효성 검사-호스트 이름이 올바르고 장치가 IoT Hub에 있으며 azureiotsecurity 쌍 모듈이 있는지 확인 합니다. | 인증 구성 유효성 검사-호스트 이름이 올바르고 장치가 IoT Hub에 있으며 azureiotsecurity 쌍 모듈이 있는지 확인 합니다. |
| 인증 | 누락 된 구성 | *Authentication.config* 파일에 구성이 없습니다. 오류 메시지에는 누락 된 키가 명시 되어야 합니다. | 파일 *의LocalConfiguration.js* 에 누락 된 키를 추가 합니다. | *Authentication.config* 파일에 누락 된 키를 추가 하는 방법에 대 한 자세한 내용은 [c #-localconfig-reference](azure-iot-security-local-configuration-csharp.md) 를 참조 하세요. |
| 인증 | 구성 구문 분석 하지 못함 | 구성 값을 구문 분석할 수 없습니다. 오류 메시지에는 구문 분석할 수 없는 키가 명시 되어야 합니다. 값이 예상 된 형식이 아니거나 값이 범위를 벗어났습니다. 구성 값을 구문 분석할 수 없습니다. | 파일의 **LocalConfiguration.js** 에서 키 값을 수정 합니다. | 스키마와 일치 하도록 **Authentication.config** 파일의 키 값을 수정 하는 방법에 대 한 자세한 내용은 [cs-localconfig-reference](azure-iot-security-local-configuration-c.md) 를 참조 하세요.|

## <a name="next-steps"></a>다음 단계

- IoT 서비스에 대 한 Defender [개요](overview.md) 를 참조 하십시오.
- IoT [아키텍처용](architecture.md) Defender에 대 한 자세한 정보
- IoT [서비스용](quickstart-onboard-iot-hub.md) Defender 사용
- IoT 서비스용 Defender에 대 한 [FAQ](resources-frequently-asked-questions.md) 읽기
- [원시 보안 데이터](how-to-security-data-access.md)에 액세스하는 방법을 알아봅니다.
- [권장 사항](concept-recommendations.md) 이해
- 보안 [경고](concept-security-alerts.md) 이해
