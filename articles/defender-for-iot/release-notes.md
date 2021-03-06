---
title: Azure Defender for IoT의 새로운 기능
description: 이 문서에서는 최신 Defender for IoT 릴리스의 새로운 기능을 확인할 수 있습니다.
ms.topic: overview
ms.date: 03/14/2021
ms.openlocfilehash: 7d8c4ebfc55c80878b780fe9f663aaec62a75f9d
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382915"
---
# <a name="whats-new-in-azure-defender-for-iot"></a>Azure Defender for IoT의 새로운 기능이란?

이 문서에는 Defender for IoT의 새로운 기능과 향상된 기능이 나와 있습니다.

언급된 기능은 미리 보기에 있습니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
## <a name="march-2021"></a>2021년 3월

### <a name="sensor---enhanced-custom-alert-rules-public-preview"></a>센서 - 향상된 사용자 지정 경고 규칙(공개 미리 보기)

사용자 지정 경고 규칙은 이제 네트워크 활동이 검색된 일, 일 그룹 및 기간을 기준으로 만들 수 있습니다.  경고 이벤트가 발생하는 시간에 따라 경고 심각도가 파생되는 경우 등에는 일 및 시간 규칙 조건을 사용하는 것이 유용합니다. 예를 들어 주말 또는 저녁에 네트워크 활동이 검색되면 심각도가 높은 경고를 트리거하는 사용자 지정 규칙을 만듭니다.

이 기능은 버전 10.2 릴리스의 센서에서 사용할 수 있습니다.

### <a name="on-premises-management-console---export-alerts-public-preview"></a>온-프레미스 관리 콘솔 - 경고 내보내기(공개 미리 보기)

경고 정보는 이제 온-프레미스 관리 콘솔에서 .csv 파일로 내보낼 수 있습니다. 검색된 모든 경고의 정보를 내보내거나 필터링된 보기를 기반으로 하여 정보를 내보낼 수 있습니다.

이 기능은 버전 10.2 릴리스의 온-프레미스 관리 콘솔에서 사용할 수 있습니다.

### <a name="add-second-network-interface-to-on-premises-management-console-public-preview"></a>온-프레미스 관리 콘솔에 두 번째 네트워크 인터페이스 추가(공개 미리 보기)

이제 두 번째 네트워크 인터페이스를 온-프레미스 관리 콘솔에 추가하여 배포의 보안을 향상시킬 수 있습니다. 이 기능을 사용하면 온-프레미스 관리에서 센서를 하나의 보안 네트워크에 연결하고, 사용자가 별도의 두 번째 네트워크 인터페이스를 통해 온-프레미스 관리 콘솔에 액세스할 수 있습니다.

이 기능은 버전 10.2 릴리스의 온-프레미스 관리 콘솔에서 사용할 수 있습니다.
### <a name="device-builder---new-micro-agent-public-preview"></a>디바이스 빌더 - 새 마이크로 에이전트(공개 미리 보기)

새 디바이스 빌더 모듈을 사용할 수 있습니다. 마이크로 에이전트라고 하는 모듈에서 수행할 수 있는 기능은 다음과 같습니다.

- **Azure IoT Hub 및 Azure Defender for IoT와 통합** - Azure IoT Hub 및 Azure Defender for IoT에서 제공하는 모니터링 옵션과 통합하여 더 강력한 엔드포인트 보안을 IoT 디바이스에 직접 빌드합니다.
- **표준 IoT 운영 체제를 지원하는 유연한 배포 옵션** - Linux 및 Azure RTOS와 같은 표준 IoT 운영 체제를 지원하여 이진 패키지 또는 수정 가능한 소스 코드로 배포할 수 있습니다.
- **OS 커널 종속성이 없는 최소 리소스 요구 사항** - 공간이 작고, CPU 사용량이 낮으며, OS 커널 종속성이 없습니다.
- **보안 상태 관리** – IoT 디바이스의 보안 상태를 사전에 모니터링합니다.
- **연속 실시간 IoT/OT 위협 탐지** - 봇네트, 무차별 암호 대입 시도, 암호화 마이너 및 의심스러운 네트워크 활동과 같은 위협을 탐지합니다.

더 이상 사용되지 않는 Defender-IoT-micro-agent 설명서는 *디바이스 빌더용 에이전트 기반 솔루션 > 클래식* 폴더로 이동됩니다.

이 기능 세트는 현재 공개 미리 보기 클라우드 릴리스에서 사용할 수 있습니다.

## <a name="january-2021"></a>2021년 1월

- [보안](#security)
- [온보딩](#onboarding)
- [유용성](#usability)
- [기타 업데이트](#other-updates)
### <a name="security"></a>보안

이 릴리스에서는 인증서 및 암호 복구 기능이 향상되었습니다.

#### <a name="certificates"></a>인증서
  
이 버전에서 수행할 수 있는 작업은 다음과 같습니다.

- SSL 인증서를 센서 및 온-프레미스 관리 콘솔에 직접 업로드합니다.
- 온-프레미스 관리 콘솔과 연결된 센서 간, 그리고 관리 콘솔과 고가용성 관리 콘솔 간에 유효성 검사를 수행합니다. 유효성 검사는 만료 날짜, 루트 CA 인증 신뢰성 및 인증서 해지 목록을 기반으로 합니다.  유효성 검사가 실패하면 세션이 계속되지 않습니다.

업그레이드의 경우

- 업그레이드하는 동안 SSL 인증서 또는 유효성 검사 기능이 변경되지 않습니다.
- 업그레이드 후 센서 및 온-프레미스 관리 콘솔 관리 사용자는 SSL 인증서를 대체하거나 시스템 설정, SSL 인증서 창에서 SSL 인증서 유효성 검사를 활성화할 수 있습니다.  

새로 설치하는 경우

- 처음 로그인하는 동안 사용자는 SSL 인증서(권장) 또는 로컬로 생성된 자체 서명된 인증서(권장하지 않음)를 사용해야 합니다.
- 새로 설치하는 경우 인증서 유효성 검사는 기본적으로 설정됩니다.

#### <a name="password-recovery"></a>암호 복구
  
센서 및 온-프레미스 관리 콘솔 관리자는 이제 Azure Defender for IoT 포털에서 암호를 복구할 수 있습니다. 이전에는 지원 팀이 개입하여 암호를 복구해야 했습니다.

### <a name="onboarding"></a>온보딩

#### <a name="on-premises-management-console---committed-devices"></a>온-프레미스 관리 콘솔 - 커밋된 디바이스

온-프레미스 관리 콘솔에 처음 로그인한 후에는 이제 사용자가 활성화 파일을 업로드해야 합니다. 이 파일에는 조직 네트워크에서 모니터링하는 디바이스의 집계 수가 포함되어 있습니다. 이 숫자를 커밋된 디바이스 수라고 합니다.
커밋된 디바이스는 활성화 파일이 생성되는 Azure Defender for IoT 포털에서 온보딩 프로세스 중에 정의됩니다.
활성화 파일을 업로드하려면 처음 사용자와 업그레이드하는 사용자가 필요합니다.
초기 활성화 후 네트워크에서 검색된 디바이스 수가 커밋된 디바이스 수를 초과할 수 있습니다. 예를 들어 더 많은 센서를 관리 콘솔에 연결하는 경우 이 이벤트가 발생할 수 있습니다. 검색된 디바이스 수와 커밋된 디바이스 수가 일치하지 않으면 관리 콘솔에 경고가 표시됩니다. 이 이벤트가 발생하면 새 활성화 파일을 업로드해야 합니다.

#### <a name="pricing-page-options"></a>가격 책정 페이지 옵션

가격 책정 페이지에서 Azure Defender for IoT에 대한 새 구독을 온보딩하고, 네트워크에서 커밋된 디바이스를 정의할 수 있습니다.  
또한 이제 가격 책정 페이지에서 센서와 연결된 기존 구독을 관리하고, 디바이스 커밋을 업데이트할 수 있습니다.

#### <a name="view-and-manage-onboarded-sensors"></a>온보딩된 센서 보기 및 관리

새 사이트 및 센서 포털 페이지에서 수행할 수 있는 작업은 다음과 같습니다.

- 센서에 대한 설명 정보를 추가합니다. 예를 들어 센서와 연결된 영역 또는 자유 텍스트 태그가 있습니다.
- 센서 정보를 보고 필터링합니다. 예를 들어 클라우드에 연결되거나 로컬로 관리되는 센서에 대한 세부 정보를 보거나 특정 영역의 센서에 대한 정보를 볼 수 있습니다.  

### <a name="usability"></a>사용 편의성

#### <a name="azure-sentinel-new-connector-page"></a>Azure Sentinel 새 커넥터 페이지

Azure Sentinel의 Azure Defender for IoT 데이터 커넥터 페이지가 다시 디자인되었습니다. 데이터 커넥터는 이제 IoT Hubs가 아닌 구독을 기반으로 하므로 고객이 Azure Sentinel에 대한 구성 연결을 더 효율적으로 관리할 수 있습니다.

#### <a name="azure-portal-permission-updates"></a>Azure Portal 권한 업데이트  

보안 읽기 권한자 및 보안 관리자 지원이 추가되었습니다.

### <a name="other-updates"></a>기타 업데이트

#### <a name="access-group---zone-permissions"></a>액세스 그룹 - 영역 권한
  
온-프레미스 관리 콘솔 액세스 그룹 규칙에는 특정 영역에 대한 액세스 권한을 부여하는 옵션이 포함되지 않습니다. 사이트, 지역 및 비즈니스 단위를 사용하는 규칙에 대한 정의는 변경되지 않습니다.   업그레이드 후에 특정 영역에 대한 액세스를 허용하는 규칙이 포함된 액세스 그룹에서 모든 영역을 포함하여 부모 사이트에 대한 액세스를 허용하도록 수정됩니다.

#### <a name="terminology-changes"></a>용어 변경

'자산'이라는 용어는 센서 및 온-프레미스 관리 콘솔, 보고서 및 기타 솔루션 인터페이스에서 '디바이스'로 이름이 변경되었습니다.
센서 및 온-프레미스 관리 콘솔 경고에서 '이 이벤트 관리'라는 용어는 '수정 단계'로 명명되었습니다.

## <a name="next-steps"></a>다음 단계

[Defender for IoT 시작](getting-started.md)
