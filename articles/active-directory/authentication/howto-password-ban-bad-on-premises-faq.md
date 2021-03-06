---
title: 온-프레미스 Azure AD 암호 보호 FAQ
description: 온-프레미스 Active Directory Domain Services 환경에서 Azure AD 암호 보호에 대 한 질문과 대답 검토
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f80990854fd0c584d8e6582fdf35108e67d9202b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625131"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Azure AD 암호 보호 온-프레미스 질문과 대답

이 섹션에서는 Azure AD 암호 보호와 관련 하 여 자주 묻는 많은 질문에 대 한 답변을 제공 합니다.

## <a name="general-questions"></a>일반적인 질문

**Q: 보안 암호를 선택 하는 방법에 대해 사용자에 게 제공 해야 하는 지침은 무엇입니까?**

이 항목에 대한 Microsoft의 현재 지침은 다음 링크에서 찾을 수 있습니다.

[Microsoft 암호 지침](https://www.microsoft.com/research/publication/password-guidance)

**Q: 온-프레미스 Azure AD 암호 보호는 공용이 아닌 클라우드에서 지원 되나요?**

온-프레미스 Azure AD 암호 보호는 공용 클라우드와 Arlington 클라우드에서 지원 됩니다. 다른 클라우드의 가용성에 대 한 날짜가 발표 되지 않았습니다.

Azure AD portal은 지원 되지 않는 클라우드에서도 온-프레미스 관련 "Windows Server Active Directory에 대 한 암호 보호" 구성을 수정할 수 있습니다. 이러한 변경 내용은 지속 되지만 그렇지 않은 경우에는 적용 되지 않습니다. 지원 되지 않는 클라우드에서 온-프레미스 프록시 에이전트 또는 포리스트 등록이 지원 되지 않으므로 이러한 등록 시도는 항상 실패 합니다.

**Q: 온-프레미스 사용자의 하위 집합에 Azure AD 암호 보호 혜택을 적용 하려면 어떻게 해야 하나요?**

지원되지 않습니다. Azure AD 암호 보호가 배포되고 사용하도록 설정되면 모든 사용자가 차별 없이 동등한 보안 혜택을 받습니다.

**Q: 암호 변경 및 암호 설정 (또는 다시 설정)의 차이점은 무엇 인가요?**

암호 변경은 사용자가 이전 암호를 알고 있는 사용자를 증명 한 후 새 암호를 선택 하는 경우입니다. 예를 들어 암호 변경은 사용자가 Windows에 로그인 할 때 발생 하는 것으로, 새 암호를 선택 하 라는 메시지가 표시 됩니다.

암호 집합 (암호 재설정이 라고도 함)은 관리자가 Active Directory 사용자 및 컴퓨터 관리 도구를 사용 하는 등의 방법으로 계정의 암호를 새 암호로 바꿀 때입니다. 이 작업을 수행 하려면 높은 수준의 권한 (일반적으로 도메인 관리자)이 필요 하며, 작업을 수행 하는 사용자는 일반적으로 이전 암호를 알지 못합니다. 지원 센터 시나리오에서는 암호를 잊어버린 사용자를 지원할 때 처럼 종종 암호 집합을 수행 합니다. 또한 암호를 사용 하 여 처음으로 새 사용자 계정을 만들 때 암호 설정 이벤트가 표시 됩니다.

암호 유효성 검사 정책은 암호 변경 또는 설정의 수행 여부에 관계 없이 동일 하 게 동작 합니다. Azure AD 암호 보호 DC 에이전트 서비스는 다른 이벤트를 기록 하 여 암호 변경 또는 설정 작업이 수행 되었는지 여부를 알려 줍니다.  [AZURE AD 암호 보호 모니터링 및 로깅을](./howto-password-ban-bad-on-premises-monitor.md)참조 하세요.

**Q: Azure AD 암호 보호는 설치 된 후 기존 암호의 유효성을 검사 하나요?**

아니요-Azure AD 암호 보호는 암호 변경 또는 설정 작업 중에 일반 텍스트 암호에 대해서만 암호 정책을 적용할 수 있습니다. Active Directory에서 암호를 수락 하면 해당 암호의 인증 프로토콜 관련 해시만 유지 됩니다. 일반 텍스트 암호는 유지 되지 않으므로 Azure AD 암호 보호는 기존 암호의 유효성을 검사할 수 없습니다.

Azure AD 암호 보호의 초기 배포 후에는 모든 사용자와 계정이 기존 암호가 일반적으로 시간에 따라 만료 되므로 Azure AD 암호 보호의 유효성을 검사 하는 암호를 사용 하기 시작 합니다. 원하는 경우 사용자 계정 암호의 일회성 수동 만료로이 프로세스의 가속화를 설정할 수 있습니다.

"암호 사용 기간 제한 없음"으로 구성 된 계정은 수동 만료가 완료 되지 않는 한 강제로 암호를 변경 하지 않습니다.

**Q: Active Directory 사용자 및 컴퓨터 관리 스냅인을 사용 하 여 약한 암호를 설정 하려고 할 때 중복 된 암호 거부 이벤트가 기록 되는 이유는 무엇 인가요?**

Active Directory 사용자 및 컴퓨터 관리 스냅인은 먼저 Kerberos 프로토콜을 사용 하 여 새 암호를 설정 하려고 시도 합니다. 오류가 발생 하면 스냅인은 레거시 (SAM RPC) 프로토콜을 사용 하 여 두 번째 시도로 암호를 설정 합니다 (사용 되는 특정 프로토콜은 중요 하지 않음). Azure AD 암호 보호에서 새 암호가 약한 것으로 간주 되는 경우이 스냅인 동작은 두 개의 암호 재설정 거부 이벤트 집합을 기록 합니다.

**Q: Azure AD 암호 보호 암호 유효성 검사 이벤트가 빈 사용자 이름으로 기록 되는 이유는 무엇 인가요?**

Active Directory는 [NetValidatePasswordPolicy](/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) api를 사용 하는 등의 방법으로 도메인의 현재 암호 복잡성 요구 사항을 통과 하는지 확인 하기 위해 암호를 테스트 하는 기능을 지원 합니다. 이러한 방식으로 암호의 유효성을 검사 하는 경우 테스트에는 Azure AD 암호 보호와 같이 암호 필터 dll 기반 제품의 유효성 검사도 포함 되지만 지정 된 암호 필터 dll에 전달 된 사용자 이름은 비어 있습니다. 이 시나리오에서 Azure AD 암호 보호는 현재 영향을 받은 암호 정책을 사용 하 여 암호의 유효성을 검사 하 고 결과를 캡처하기 위해 이벤트 로그 메시지를 발행 하지만 이벤트 로그 메시지에는 빈 사용자 이름 필드가 있습니다.

**Q: 다른 암호 필터 기반 제품과 함께 Azure AD 암호 보호를 설치 하는 것이 지원 되나요?**

예. 등록된 여러 암호 필터 DLL에 대한 지원은 Windows의 핵심 기능이며 Azure AD 암호 보호와 관련이 없습니다. 암호가 수락되기 전에 모든 등록된 암호 필터 dll에 동의해야 합니다.

**Q: Azure를 사용 하지 않고 내 Active Directory 환경에서 Azure AD 암호 보호를 배포 하 고 구성 하려면 어떻게 해야 하나요?**

지원되지 않습니다. Azure AD 암호 보호는 온-프레미스 Active Directory 환경으로 지원이 확장되는 Azure 기능입니다.

**Q: Active Directory 수준에서 정책의 내용을 수정 하려면 어떻게 해야 하나요?**

지원되지 않습니다. 정책은 Azure AD 포털을 사용 해야만 관리할 수 있습니다. 이전 질문도 참조하세요.

**Q: sysvol 복제에 DFSR이 필요한가요?**

FRS(DFSR에 대한 선행 기술)는 대부분의 알려진 문제를 포함하며, 최신 버전의 Windows Server Active Directory에서 완전히 지원되지 않습니다. Azure AD 암호 보호의 제로 테스트는 FRS 구성 도메인에서 수행됩니다.

자세한 내용은 다음 문서를 참조하세요.

[DFSR로 sysvol 복제를 마이그레이션하는 사례](/archive/blogs/askds/the-case-for-migrating-sysvol-to-dfsr)

[FRS의 경우 종료가 가깝습니다.](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

도메인이 아직 DFSR을 사용 하지 않는 경우 Azure AD 암호 보호를 설치 하기 전에 DFSR을 사용 하도록 마이그레이션해야 합니다. 자세한 내용은 다음 링크를 참조 하세요.

[SYSVOL 복제 마이그레이션 가이드: FRS에서 DFS 복제으로](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> Azure AD 암호 보호 DC 에이전트 소프트웨어는 현재 sysvol 복제를 위해 여전히 FRS를 사용 하는 도메인의 도메인 컨트롤러에 설치 되지만이 환경에서는 소프트웨어가 제대로 작동 하지 않습니다. 추가 부정적 부작용에는 복제에 실패 한 개별 파일이 포함 되며 sysvol 복원 절차는 성공 하는 것 처럼 보이지만 자동으로 모든 파일을 복제 하지 못합니다. DFSR의 내재 된 혜택을 제공 하 고 Azure AD 암호 보호 배포의 차단을 해제 하기 위해 가능한 한 빨리 DFSR을 사용 하려면 도메인을 마이그레이션해야 합니다. 이후 버전의 소프트웨어는 여전히 FRS를 사용 하는 도메인에서 실행 되는 경우 자동으로 사용 하지 않도록 설정 됩니다.

**Q: 도메인 sysvol 공유에서 기능에 필요한 디스크 공간은 어느 정도 인가요?**

정확한 공간 사용량은 Microsoft 전역 금지 목록의 금지된 토큰의 수 및 길이, 테넌트당 사용자 지정 목록, 암호화 오버헤드 등과 같은 요소에 따라 다릅니다. 이러한 목록의 내용은 향후에 증가할 수 있습니다. 이점에 유의하면 이 기능을 위해 도메인 sysvol 공유에 최소 5메가바이트의 공간이 필요한 것으로 예측할 수 있습니다.

**Q: DC 에이전트 소프트웨어를 설치하거나 업그레이드하는 데 다시 부팅이 필요한 이유는 무엇인가요?**

이 요구 사항은 핵심 Windows 동작으로 인해 발생할 수 있습니다.

**Q: 특정 프록시 서버를 사용하도록 DC 에이전트를 구성하는 방법이 있나요?**

아니요. 프록시 서버는 상태 비저장 서버이므로 어떤 프록시 서버를 사용하는지는 중요하지 않습니다.

**Q: Azure AD 암호 보호 프록시 서비스를 Azure AD Connect와 같은 다른 서비스와 함께 배포 하는 것이 정상 인가요?**

예. Azure AD 암호 보호 프록시 서비스와 Azure AD Connect는 서로 직접적으로 충돌하지 않습니다.

아쉽게도 Azure AD 암호 보호 프록시 소프트웨어에 의해 설치 되는 Microsoft Azure AD Connect Agent 업데이트 서비스와 [Azure Active Directory 응용 프로그램 프록시](../manage-apps/application-proxy.md) 소프트웨어에 의해 설치 되는 서비스 버전 사이에 비 호환성이 있습니다. 이러한 비 호환성으로 인해 에이전트 업데이트 서비스에서 소프트웨어 업데이트를 위해 Azure에 연결 하지 못할 수 있습니다. Azure AD 암호 보호 프록시를 설치 하 고 동일한 컴퓨터에 Azure Active Directory 응용 프로그램 프록시 하는 것은 권장 되지 않습니다.

**Q: DC 에이전트와 프록시를 설치 하 고 등록 해야 하는 순서는 무엇입니까?**

프록시 에이전트 설치, DC 에이전트 설치, 포리스트 등록 및 프록시 등록 순서가 지원 됩니다.

**Q: 도메인 컨트롤러에서이 기능을 배포 하는 데의 한 성능 저하에 대해 걱정 해야 하나요?**

Azure AD 암호 보호 DC 에이전트 서비스는 기존의 정상적인 Active Directory 배포에서 도메인 컨트롤러 성능에 크게 영향을 주지 않아야 합니다.

대부분의 Active Directory 배포에서 암호 변경 작업은 지정된 도메인 컨트롤러의 전체 워크로드에서 작은 비율을 차지합니다. 예를 들어, 10,000개의 사용자 계정이 있고 MaxPasswordAge 정책이 30일로 설정된 Active Directory 도메인이 있다고 가정합니다. 평균적으로 이 도메인에서는 매일 10000/30=~333번의 암호 변경 작업이 진행되며, 이 수치는 단일 도메인 컨트롤러라고 해도 많지 않은 작업 수입니다. 이러한 단일 DC에 대한 ~333번의 암호 변경이 1시간 동안 수행되는 최악의 시나리오를 고려해보세요. 예를 들어 이 시나리오는 많은 직원이 모두 월요일 아침에 근무하러 오게 될 때 발생할 수 있습니다. 이러한 경우에도, ~333/60분 = 분당 6번 암호 변경이 확인되며, 역시 이 수치는 큰 부하가 아닙니다.

그러나 현재 도메인 컨트롤러가 이미 성능 제한 수준에서 실행되고 있는 경우(예: CPU, 디스크 공간, 디스크 I/O 등에 따라 혼합됨) 이 기능을 배포하기 전에 추가 도메인 컨트롤러를 추가하거나 사용 가능한 디스크 공간을 확장하는 것이 좋습니다. 위의 sysvol 디스크 공간 사용량에 대한 질문도 참조하세요.

**Q: 내 도메인의 일부 Dc에서 Azure AD 암호 보호를 테스트 하려고 합니다. 사용자 암호를 변경 하 여 특정 Dc를 사용할 수 있나요?**

아니요. Windows 클라이언트 OS는 사용자가 암호를 변경할 때 사용되는 도메인 컨트롤러를 제어합니다. 도메인 컨트롤러는 Active Directory 사이트 및 서브넷 할당, 환경 특정 네트워크 구성 등의 요소에 따라 선택 됩니다. Azure AD 암호 보호는 이러한 요인을 제어 하지 않으며 사용자 암호를 변경 하기 위해 선택한 도메인 컨트롤러에 영향을 주지 않습니다.

부분적으로 이러한 목표에 도달하는 한 가지 방법은 지정된 Active Directory 사이트의 모든 도메인 컨트롤러에서 Azure AD 암호 보호를 배포하는 것입니다. 이 방법은 해당 사이트에 할당된 Windows 클라이언트와 해당 클라이언트에 로그인하고 암호를 변경하는 사용자를 적절히 검사합니다.

**Q: PDC (주 도메인 컨트롤러)에만 Azure AD 암호 보호 DC 에이전트 서비스를 설치 하는 경우 도메인의 다른 모든 도메인 컨트롤러도 보호 되나요?**

아니요. 지정된 비 PDC 도메인 컨트롤러에서 사용자 암호가 변경되면 일반 텍스트 암호가 PDC로 절대 전송되지 않습니다(이 생각은 일반적인 오해임). 지정된 DC에서 새 암호가 수락되면 해당 DC는 해당 암호를 사용하여 암호의 다양한 인증-프로토콜 관련 해시를 만든 후 해당 해시를 디렉터리에 유지합니다. 일반 텍스트 암호는 유지되지 않습니다. 그런 후에 업데이트된 해시가 PDC로 복제됩니다. 경우에 따라 네트워크 토폴로지 및 Active Directory 사이트 디자인과 같은 다양한 요인에 따라 PDC에서 직접 변경될 수 있습니다. (이전 질문을 참조하세요.)

요약하자면, PDC에서 Azure AD 암호 보호 DC 에이전트 서비스를 배포하려면 도메인에서 100%의 기능 보안 검사에 도달해야 합니다. PDC에만 기능을 배포하면 도메인의 다른 DC는 Azure AD 암호 보호 보안 이점을 얻을 수 없습니다.

**Q: 온-프레미스 Active Directory 환경에 에이전트가 설치 된 후에도 사용자 지정 스마트 잠금이 작동 하지 않는 이유는 무엇 인가요?**

사용자 지정 스마트 잠금은 Azure AD 에서만 지원 됩니다. Azure AD 포털에서 사용자 지정 스마트 잠금 설정을 변경 해도 에이전트가 설치 된 경우에도 온-프레미스 Active Directory 환경에는 영향을 주지 않습니다.

**Q: System Center Operations Manager 관리 팩은 Azure AD 암호 보호에 사용할 수 있나요?**

아니요.

**Q: 정책을 감사 모드로 구성 했더라도 Azure AD에서 여전히 약한 암호를 거부 하는 이유는 무엇 인가요?**

감사 모드는 온-프레미스 Active Directory 환경 에서만 지원 됩니다. Azure AD는 암호를 평가할 때 암시적으로 항상 "적용" 모드로 전환 됩니다.

**Q: Azure AD 암호 보호에서 암호를 거부 하는 경우 내 사용자는 기존 Windows 오류 메시지를 볼 수 있습니다. 사용자가 정말로 무엇이 발생 했는지 알 수 있도록이 오류 메시지를 사용자 지정할 수 있나요?**

아니요. 도메인 컨트롤러에서 암호를 거부 하는 경우 사용자에 게 표시 되는 오류 메시지는 도메인 컨트롤러가 아니라 클라이언트 컴퓨터에서 제어 합니다. 이 동작은 암호가 기본 Active Directory 암호 정책에 의해 거부 되거나 Azure AD 암호 보호와 같은 암호 필터 기반 솔루션에 의해 거부 되는지 여부에 따라 수행 됩니다.

## <a name="password-testing-procedures"></a>암호 테스트 절차

소프트웨어의 적절 한 작동을 확인 하 고 [암호 평가 알고리즘](concept-password-ban-bad.md#how-are-passwords-evaluated)을 보다 잘 이해 하기 위해 다양 한 암호에 대 한 몇 가지 기본적인 테스트를 수행 하는 것이 좋습니다. 이 섹션에서는 반복 가능한 결과를 생성 하도록 설계 된 이러한 테스트에 대 한 방법을 간략하게 설명 합니다.

이러한 단계를 수행 해야 하는 이유는 무엇 인가요? 온-프레미스 Active Directory 환경에서 암호를 제어 하 고 반복 가능한 테스트를 수행 하기 어려운 몇 가지 요인이 있습니다.

* 암호 정책은 Azure에서 구성 및 유지 되 고, 정책 복사본은 폴링 메커니즘을 사용 하 여 온-프레미스 DC 에이전트에 의해 주기적으로 동기화 됩니다. 이 폴링 주기에 내재 된 대기 시간으로 인해 혼란이 발생할 수 있습니다. 예를 들어 Azure에서 정책을 구성 하지만 DC 에이전트와 동기화 하는 것을 잊은 경우 테스트에서 예상 된 결과가 생성 되지 않을 수 있습니다. 현재 폴링 간격은 1 시간에 한 번만 하드 코딩 되지만 정책 변경 사이에 시간을 대기 하는 것은 대화형 테스트 시나리오에 적합 하지 않습니다.
* 새 암호 정책이 도메인 컨트롤러와 동기화 되 면 다른 도메인 컨트롤러에 복제 하는 동안 더 많은 대기 시간이 발생 합니다. 최신 버전의 정책이 아직 수신 되지 않은 도메인 컨트롤러에 대해 암호 변경을 테스트 하면 이러한 지연으로 인해 예기치 않은 결과가 발생할 수 있습니다.
* 사용자 인터페이스를 통해 암호 변경을 테스트 하면 결과에 자신감을 가지는 것이 어렵습니다. 예를 들어, 사용자 인터페이스에 잘못 된 암호를 잘못 입력 하는 것이 쉽습니다. 특히 대부분의 암호 사용자 인터페이스가 사용자 입력 (예: Windows Ctrl-Alt-Delete-> 암호 UI)을 숨기는 것 이기 때문입니다.
* 도메인에 가입 된 클라이언트에서 암호 변경을 테스트할 때 사용 되는 도메인 컨트롤러를 엄격 하 게 제어할 수는 없습니다. Windows 클라이언트 OS는 Active Directory 사이트 및 서브넷 할당, 환경 특정 네트워크 구성 등의 요소에 따라 도메인 컨트롤러를 선택 합니다.

이러한 문제를 방지 하기 위해 다음 단계는 도메인 컨트롤러에 로그인 하는 동안 암호 재설정의 명령줄 테스트를 기반으로 합니다.

> [!WARNING]
> 이러한 절차는 테스트 환경 에서만 사용 해야 합니다. 들어오는 모든 암호 변경 및 재설정은 DC 에이전트 서비스가 중지 되는 동안 유효성 검사 없이 수락 되 고, 또한 도메인 컨트롤러에 로그인 하는 데 내재 된 위험이 발생 하지 않기 때문입니다.

다음 단계에서는 하나 이상의 도메인 컨트롤러에 DC 에이전트를 설치 하 고, 프록시를 하나 이상 설치 하 고 프록시와 포리스트를 모두 등록 했다고 가정 합니다.

1. DC 에이전트 소프트웨어가 설치 되어 있고 다시 부팅 된 도메인 관리자 자격 증명 (또는 테스트 사용자 계정을 만들고 암호를 다시 설정할 수 있는 권한이 있는 다른 자격 증명)을 사용 하 여 도메인 컨트롤러에 로그온 합니다.
1. 이벤트 뷰어를 열고 [DC 에이전트 관리자 이벤트 로그](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log)로 이동 합니다.
1. 관리자 권한으로 명령 프롬프트 창을 엽니다.
1. 암호 테스트를 위한 테스트 계정 만들기

   사용자 계정을 만드는 방법에는 여러 가지가 있지만 반복적인 테스트 주기 동안 쉽게 수행할 수 있는 방법으로 명령줄 옵션이 제공 됩니다.

   ```text
   net.exe user <testuseraccountname> /add <password>
   ```

   아래의 설명을 위해 "ContosoUser" 이라는 테스트 계정을 만들었다고 가정 합니다. 예를 들면 다음과 같습니다.

   ```text
   net.exe user ContosoUser /add <password>
   ```

1. 웹 브라우저를 열고 (도메인 컨트롤러 대신 별도의 장치를 사용 해야 할 수 있음), [Azure Portal](https://portal.azure.com)에 로그인 하 고 Azure Active Directory > 보안 > 인증 > 방법으로 암호 보호를 검색 합니다.
1. 수행 하려는 테스트에 대 한 Azure AD 암호 보호 정책을 필요에 따라 수정 합니다.  예를 들어, 적용 또는 감사 모드를 구성 하거나 사용자 지정 금지 된 암호 목록에서 금지 된 약관 목록을 수정 하도록 결정할 수 있습니다.
1. DC 에이전트 서비스를 중지 했다가 다시 시작 하 여 새 정책을 동기화 합니다.

   이 단계는 다양 한 방법으로 수행할 수 있습니다. 한 가지 방법은 Azure AD 암호 보호 DC 에이전트 서비스를 마우스 오른쪽 단추로 클릭 하 고 "다시 시작"을 선택 하 여 서비스 관리 관리 콘솔을 사용 하는 것입니다. 다음과 같이 명령 프롬프트 창에서 다른 방법을 수행할 수도 있습니다.

   ```text
   net stop AzureADPasswordProtectionDCAgent && net start AzureADPasswordProtectionDCAgent
   ```
    
1. 이벤트 뷰어를 확인 하 여 새 정책이 다운로드 되었는지 확인 합니다.

   DC 에이전트 서비스를 중지 하 고 시작할 때마다 종료 시 발생 한 2 30006 이벤트를 확인 해야 합니다. 첫 번째 30006 이벤트는 sysvol 공유의 디스크에 캐시 된 정책을 반영 합니다. 두 번째 30006 이벤트 (있는 경우)는 업데이트 된 테 넌 트 정책 날짜를 가져야 하며,이 경우 Azure에서 다운로드 한 정책이 반영 됩니다. 현재 테 넌 트 정책 날짜 값은 정책이 Azure에서 다운로드 된 대략적인 타임 스탬프를 표시 하도록 코딩 되어 있습니다.
   
   두 번째 30006 이벤트가 표시 되지 않는 경우 계속 하기 전에 문제를 해결 해야 합니다.
   
   30006 이벤트는 다음 예와 유사 하 게 표시 됩니다.
 
   ```text
   The service is now enforcing the following Azure password policy.

   Enabled: 1
   AuditOnly: 0
   Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
   Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
   Enforce tenant policy: 1
   ```

   예를 들어 적용 모드와 감사 모드를 변경 하면 AuditOnly 플래그가 수정 됩니다 (위의 정책은 AuditOnly = 0 인 경우는 적용 모드). 사용자 지정 금지 된 암호 목록의 변경 내용은 위의 30006 이벤트에 직접 반영 되지 않으며 보안상의 이유로 다른 위치에 기록 되지 않습니다. 이러한 변경 후 Azure에서 정책을 다운로드 하면 수정 된 사용자 지정 금지 된 암호 목록도 포함 됩니다.

1. 테스트 사용자 계정에서 새 암호를 다시 설정 하 여 테스트를 실행 합니다.

   이 단계는 다음과 같이 명령 프롬프트 창에서 수행할 수 있습니다.

   ```text
   net.exe user ContosoUser <password>
   ```

   명령을 실행 한 후 이벤트 뷰어를 살펴보면 명령의 결과에 대 한 자세한 정보를 볼 수 있습니다. 암호 유효성 검사 결과 이벤트는 [DC 에이전트 관리자 이벤트 로그](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log) 항목에 설명 되어 있습니다. 이러한 이벤트를 사용 하 여 net.exe 명령의 대화형 출력과 함께 테스트 결과의 유효성을 검사 합니다.

   예를 들어, Microsoft 전역 목록에서 금지 된 암호를 설정 하려고 시도 합니다. 목록에는 [문서화 되어 있지](concept-password-ban-bad.md#global-banned-password-list) 않지만 알려진 금지 된 용어에 대해서는 여기에서 테스트할 수 있습니다. 이 예에서는 정책을 적용 모드로 구성 하 고 사용자 지정 금지 된 암호 목록에 0 항을 추가 했다고 가정 합니다.

   ```text
   net.exe user ContosoUser PassWord
   The password does not meet the password policy requirements. Check the minimum password length, password complexity and password history requirements.

   More help is available by typing NET HELPMSG 2245.
   ```

   이 테스트는 암호 재설정 작업 이므로 설명서에 따라 ContosoUser 사용자에 대 한 10017 및 30005 이벤트를 확인 해야 합니다.

   10017 이벤트는 다음 예제와 같이 표시 됩니다.

   ```text
   The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   30005 이벤트는 다음 예제와 같이 표시 됩니다.

   ```text
   The reset password for the specified user was rejected because it matched at least one of the tokens present in the Microsoft global banned password list of the current Azure password policy.
 
   UserName: ContosoUser
   FullName: 
   ```

   재미 있게 되었습니다. 다른 예제를 사용해 보세요. 이번에는 정책이 감사 모드에 있는 동안 사용자 지정 금지 된 목록에 의해 차단 되는 암호를 설정 하려고 합니다. 이 예에서는 감사 모드로 정책을 구성 하 고, 사용자 지정 금지 된 암호 목록에 "lachrymose" 라는 용어를 추가 하 고, 위에 설명 된 대로 DC 에이전트 서비스를 순환 하 여 결과 새 정책을 도메인 컨트롤러에 동기화 하는 단계를 수행 했다고 가정 합니다.

   확인, 금지 된 암호의 변형을 설정 합니다.

   ```text
   net.exe user ContosoUser LaChRymoSE!1
   The command completed successfully.
   ```

   이번에는 정책이 감사 모드에 있기 때문에 성공 했습니다. ContosoUser 사용자에 대 한 10025 및 30007 이벤트를 확인 해야 합니다.

   10025 이벤트는 다음 예제와 같이 표시 됩니다.
   
   ```text
   The reset password for the specified user would normally have been rejected because it did not comply with the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   30007 이벤트는 다음 예제와 같이 표시 됩니다.

   ```text
   The reset password for the specified user would normally have been rejected because it matches at least one of the tokens present in the per-tenant banned password list of the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted.
 
   UserName: ContosoUser
   FullName: 
   ```

1. 이전 단계에 설명 된 절차를 사용 하 여 선택한 다양 한 암호를 계속 테스트 하 고 이벤트 뷰어에서 결과를 확인 합니다. Azure Portal에서 정책을 변경 해야 하는 경우 앞에서 설명한 대로 새 정책을 DC 에이전트와 동기화 해야 합니다.

Azure AD 암호 보호의 암호 유효성 검사 동작에 대 한 제어 된 테스트를 수행할 수 있는 절차를 설명 했습니다. 도메인 컨트롤러에서 직접 명령줄에서 사용자 암호를 다시 설정 하는 것은 이러한 테스트를 수행 하는 데는 이상한 방법이 될 수 있지만 앞에서 설명한 것 처럼 반복 가능한 결과를 생성 하도록 설계 되었습니다. 다양 한 암호를 테스트할 때 예상치 못한 결과를 설명 하는 데 도움이 될 수 있으므로 [암호 평가 알고리즘](concept-password-ban-bad.md#how-are-passwords-evaluated) 을 염두에 두어야 합니다.

> [!WARNING]
> 모든 테스트가 완료 되 면 테스트 목적으로 만든 모든 사용자 계정을 삭제 하는 것을 잊지 마세요.

## <a name="additional-content"></a>추가 콘텐츠

다음 링크는 핵심 Azure AD 암호 보호 설명서에는 포함되지 않지만 기능에 대한 유용한 추가 정보를 제공할 수 있습니다.

[Azure AD 암호 보호는 이제 일반 공급 됩니다.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[이메일 피싱 방지 가이드-15 부: Microsoft Azure AD 암호 보호 서비스 (온-프레미스의 경우)를 구현 합니다.](http://kmartins.com/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[이제 Azure AD 암호 보호 및 스마트 잠금이 공개 미리 보기로 제공됨](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Microsoft 프리미어\통합 지원 교육 사용 가능

Azure AD 암호 보호에 대해 자세히 알아보고 작업 환경에 배포하려는 경우 프리미어 또는 통합 지원 계약이 있는 고객을 위한 Microsoft 자동 관리 서비스의 활용할 수 있습니다. 서비스는 암호 보호 Azure Active Directory 라고 합니다. 자세한 내용은 기술 계정 관리자에게 문의하세요.

## <a name="next-steps"></a>다음 단계

여기서 답변되지 않은 온-프레미스 Azure AD 암호 보호 질문이 있으면 아래의 피드백 항목을 제출해 주세요. 감사합니다!

[Azure AD 암호 보호 배포](howto-password-ban-bad-on-premises-deploy.md)