---
title: Azure Marketplace의 보안 권장 사항 이미지 | Microsoft Docs
description: 이 문서는 마켓플레이스에 포함된 이미지에 대한 권장 사항을 제공합니다.
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: terrylan
ms.openlocfilehash: 7c317a0b4fea0c981b227bace00c1b8924fd582c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89536385"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Azure Marketplace의 보안 권장 사항 이미지

이미지는 이러한 보안 구성 권장 사항을 충족 해야 합니다. 그러면 Azure Marketplace에서 파트너 솔루션 이미지의 높은 보안 수준을 유지할 수 있습니다.

제출 하기 전에 항상 이미지에서 보안 취약성 감지를 실행 합니다. 사용자가 게시 한 이미지에서 보안 취약성을 검색 하는 경우에는 적시에 적절 한 방법으로 고객에 게 알리고 해결 방법을 알려야 합니다.

## <a name="open-source-based-images"></a>소스 기반 이미지 열기

| 범주 | 확인 |
| -------- | ----- |
| 보안                                                     | Linux 배포에 대 한 최신 보안 패치를 모두 설치 합니다.                                                                                                                                                                                                              |
| 보안                                                     | 산업 지침에 따라 특정 Linux 배포에 대 한 VM 이미지를 보호 합니다.                                                                                                                                                                                     |
| 보안                                                     | 필요한 Windows Server 역할, 기능, 서비스 및 네트워킹 포트만 포함하도록 공간을 최소화하여 공격 표면을 제한합니다.                                                                                                                                               |
| 보안                                                     | 맬웨어의 소스 코드 및 결과 VM 이미지를 검색합니다.                                                                                                                                                                                                                                   |
| 보안                                                     | VHD 이미지에는 대화형 로그인을 허용 하는 기본 암호가 없는 필요한 잠긴 계정만 포함 됩니다. 문이 없습니다.                                                                                                                                           |
| 보안                                                     | 방화벽 어플라이언스와 같은 응용 프로그램에서이 기능을 사용 하지 않는 한 방화벽 규칙을 사용 하지 않도록 설정 합니다.                                                                                                                                                                             |
| 보안                                                     | 테스트 SSH 키, 알려진 호스트 파일, 로그 파일 및 불필요 한 인증서와 같은 중요 한 정보를 모두 VHD 이미지에서 제거 합니다.                                                                                                                                       |
| 보안                                                     | LVM을 사용 하지 마십시오.                                                                                                                                                                                                                                            |
| 보안                                                     | 필요한 라이브러리의 최신 버전을 포함 합니다. </br> - OpenSSL v1.0 이상 </br> - Python 2.5 이상(Python 2.6 이상 권장) </br> - Python pyasn1 패키지(아직 설치되지 않은 경우) </br> - d.OpenSSL v 1.0 이상                                                                |
| 보안                                                     | Bash/셸 기록 항목을 지웁니다.                                                                                                                                                                                                                                             |
| 네트워킹                                                   | SSH 서버는 기본적으로 포함 합니다. 다음 옵션을 사용 하 여 SSH keep alive을 sshd config로 설정 합니다. ClientAliveInterval 180.                                                                                                                                                        |
| 네트워킹                                                   | 이미지에서 사용자 지정 네트워크 구성을 제거 합니다. Resolv.conf를 삭제 `rm /etc/resolv.conf` 합니다.                                                                                                                                                                                |
| 배포                                                   | 최신 Azure Linux 에이전트를 설치 합니다.</br> -RPM 또는 Deb 패키지를 사용 하 여 설치 합니다.  </br> - 수동 설치 프로세스를 사용할 수도 있지만 설치 프로그램 패키지가 권장되며 선호됩니다. </br> - GitHub 리포지토리에서 에이전트를 수동으로 설치하는 경우에는 먼저 `waagent` 파일을 `/usr/sbin`에 복사하고 루트로 실행합니다. </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>에이전트 구성 파일은 `/etc/waagent.conf`에 있습니다. |
| 배포                                                   | Azure 지원이 필요할 때 파트너에 게 직렬 콘솔 출력을 제공 하 고 클라우드 저장소에서 OS 디스크를 탑재 하는 데 적절 한 제한 시간을 제공할 수 있는지 확인 합니다. 다음 매개 변수를 이미지 커널 부팅 줄에 추가 `console=ttyS0 earlyprintk=ttyS0 rootdelay=300` 합니다. |
| 배포                                                   | OS 디스크에 스왑 파티션이 없습니다. Linux 에이전트에서 로컬 리소스 디스크에 생성할 스왑을 요청할 수 있습니다.         |
| 배포                                                   | OS 디스크에 대해 단일 루트 파티션을 만듭니다.      |
| 배포                                                   | 64비트 운영 체제만 해당됩니다.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Windows Server 기반 이미지

| 범주 | 확인 |
|--------- | ----- |
| 보안                                                         | 보안 OS 기본 이미지를 사용합니다. Windows Server를 기반으로 하는 모든 이미지의 원본에 사용된 VHD는 Microsoft Azure를 통해 제공되는 Windows Server OS 이미지에서 가져와야 합니다. |
| 보안                                                         | 모든 최신 보안 업데이트를 설치합니다.                                                                                                                                     |
| 보안                                                         | 응용 프로그램은 관리자, 루트 또는 관리자와 같은 제한 된 사용자 이름을 사용 하면 안 됩니다.                                                                |
| 보안                                                         | OS 하드 드라이브와 데이터 하드 드라이브 모두에 BitLocker 드라이브 암호화을 사용 하도록 설정 합니다.                                                             |
| 보안                                                         | 필요한 Windows Server 역할, 기능, 서비스 및 네트워킹 포트만 사용하도록 설정해 공간을 최소화함으로써 공격 표면을 제한합니다.                         |
| 보안                                                         | 맬웨어의 소스 코드 및 결과 VM 이미지를 검색합니다.                                                                                                                     |
| 보안                                                         | Windows Server 이미지 보안 업데이트를 자동 업데이트로 설정합니다.                                                                                                                |
| 보안                                                         | VHD 이미지에는 대화형 로그인을 허용 하는 기본 암호가 없는 필요한 잠긴 계정만 포함 됩니다. 문이 없습니다.                             |
| 보안                                                         | 방화벽 어플라이언스와 같은 응용 프로그램에서이 기능을 사용 하지 않는 한 방화벽 규칙을 사용 하지 않도록 설정 합니다.                                                               |
| 보안                                                         | HOSTS 파일, 로그 파일 및 불필요 한 인증서를 포함 하 여 VHD 이미지에서 중요 한 정보를 모두 제거 합니다.                                              |
| 배포                                                       | 64비트 운영 체제만 해당됩니다.                            |

조직에 Azure marketplace에 이미지가 없는 경우에도 이러한 권장 사항에 대해 Windows 및 Linux 이미지 구성을 확인 하는 것이 좋습니다.

