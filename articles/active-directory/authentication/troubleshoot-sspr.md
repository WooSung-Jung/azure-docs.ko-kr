---
title: 셀프 서비스 비밀번호 재설정 문제 해결 - Azure Active Directory
description: Azure Active Directory에서 셀프 서비스 암호 재설정에 대 한 일반적인 문제 및 해결 단계를 해결 하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1dee21182349108c44f9d498417d3b760ac4913
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103600865"
---
# <a name="troubleshoot-self-service-password-reset-in-azure-active-directory"></a>Azure Active Directory에서 셀프 서비스 암호 재설정 문제 해결

Azure Active Directory (Azure AD) 셀프 서비스 암호 재설정 (SSPR)을 사용 하면 사용자가 클라우드에서 자신의 암호를 재설정할 수 있습니다.

SSPR에 문제가 있는 경우 다음과 같은 문제 해결 단계 및 일반적인 오류가 유용할 수 있습니다. 이 짧은 비디오에서는 [6 가지 가장 일반적인 SSPR 최종 사용자 오류 메시지를 해결 하는 방법](https://www.youtube.com/watch?v=9RPrNVLzT8I&list=PL3ZTgFEc7LyuS8615yo39LtXR7j1GCerW&index=1)에 대해 볼 수도 있습니다.

문제에 대 한 답변을 찾을 수 없는 경우 [지원 팀](#contact-microsoft-support) 에 문의 하 여 더 많은 도움을 받을 수 있습니다.

## <a name="sspr-configuration-in-the-azure-portal"></a>Azure Portal SSPR 구성

Azure Portal에서 SSPR 옵션을 보거나 구성 하는 데 문제가 있는 경우 다음 문제 해결 단계를 검토 하세요.

### <a name="i-dont-see-the-password-reset-section-under-azure-ad-in-the-azure-portal"></a>Azure Portal의 Azure AD 섹션에서 **암호 재설정** 이 표시되지 않습니다.

작업을 수행 하는 관리자에 게 Azure AD 라이선스가 할당 되어 있지 않은 경우 **암호 다시 설정** 메뉴 옵션이 표시 되지 않습니다.

문제의 관리자 계정에 라이선스를 할당 하려면 [라이선스 관련 문제를 할당, 확인 및 해결](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses)하는 단계를 수행 합니다.

### <a name="i-dont-see-a-particular-configuration-option"></a>특정 구성 옵션이 보이지 않습니다.

UI의 많은 요소는 필요할 때까지 숨겨집니다. 특정 구성 옵션을 찾기 전에 옵션을 사용할 수 있는지 확인 합니다.

### <a name="i-dont-see-the-on-premises-integration-tab"></a>**온-프레미스 통합** 탭이 표시되지 않습니다.

온-프레미스 비밀 번호 쓰기 저장은 Azure AD Connect를 다운로드 하 고 기능을 구성한 경우에만 표시 됩니다.

자세한 내용은 [Azure AD Connect 시작](../hybrid/how-to-connect-install-express.md)을 참조 하세요.

## <a name="sspr-reporting"></a>SSPR 보고

Azure Portal에서 SSPR 보고에 문제가 있는 경우 다음 문제 해결 단계를 검토 하십시오.

### <a name="i-dont-see-any-password-management-activity-types-in-the-self-service-password-management-audit-event-category"></a>**셀프 서비스 암호 관리** 감사 이벤트 범주에 암호 관리 활동 형식이 표시 되지 않습니다.

작업을 수행하는 관리자에게 Azure AD 라이선스가 할당되지 않은 경우에 발생할 수 있습니다.

문제의 관리자 계정에 라이선스를 할당 하려면 [라이선스 관련 문제를 할당, 확인 및 해결](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses)하는 단계를 수행 합니다.

### <a name="user-registrations-show-multiple-times"></a>사용자 등록이 여러 번 표시됩니다

사용자가 등록 하면 현재 개별 이벤트로 등록 된 데이터의 각 개별 부분을 기록 합니다.

이 데이터를 집계하려고 하며 데이터를 보다 다양한 방법으로 확인하고 싶은 경우 보고서를 다운로드하고 엑셀에서 피벗 테이블로 데이터를 열 수 있습니다.

## <a name="sspr-registration-portal"></a>SSPR 등록 포털

사용자가 SSPR에 등록 하는 데 문제가 있는 경우 다음 문제 해결 단계를 검토 합니다.

### <a name="the-directory-isnt-enabled-for-password-reset-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>암호 재설정에 디렉터리를 사용할 수 없습니다. 사용자에 게 "관리자가이 기능을 사용할 수 있도록 설정 하지 않았습니다." 라는 오류가 표시 될 수 있습니다.

모든 사용자, 사용자 없음 또는 선택한 사용자 그룹에 대해 SSPR를 사용 하도록 설정할 수 있습니다. 현재 Azure Portal을 사용하여 SSPR에 대해 하나의 Azure AD 그룹만 사용하도록 설정할 수 있습니다. 더 광범위한 SSPR 배포의 일부로 중첩된 그룹이 지원됩니다. 선택한 그룹의 사용자에게 적절한 라이선스가 할당되어 있는지 확인합니다.

Azure Portal에서 **셀프 서비스 암호 재설정 사용** 구성을 *선택* 됨 또는 *모두* 로 변경 하 고 **저장** 을 선택 합니다.

### <a name="the-user-doesnt-have-an-azure-ad-license-assigned-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>사용자에 게 Azure AD 라이선스가 할당 되어 있지 않습니다. 사용자에 게 "관리자가이 기능을 사용할 수 있도록 설정 하지 않았습니다." 라는 오류가 표시 될 수 있습니다.

현재 Azure Portal을 사용하여 SSPR에 대해 하나의 Azure AD 그룹만 사용하도록 설정할 수 있습니다. 더 광범위한 SSPR 배포의 일부로 중첩된 그룹이 지원됩니다. 선택한 그룹의 사용자에게 적절한 라이선스가 할당되어 있는지 확인합니다. 이전 문제 해결 단계를 검토 하 여 필요에 따라 SSPR를 사용 하도록 설정 합니다.

또한 구성 옵션을 수행 하는 관리자에 게 라이선스가 할당 되었는지 확인 하는 문제 해결 단계를 검토 합니다. 문제의 관리자 계정에 라이선스를 할당 하려면 [라이선스 관련 문제를 할당, 확인 및 해결](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses)하는 단계를 수행 합니다.

### <a name="theres-an-error-processing-the-request"></a>요청을 처리 하는 동안 오류가 발생 했습니다.

일반적인 SSPR 등록 오류는 많은 문제로 인해 발생할 수 있지만 일반적으로 서비스 중단 또는 구성 문제로 인해 발생 합니다. SSPR 등록 프로세스를 다시 시도할 때이 일반 오류가 계속 표시 되 면 [Microsoft 지원에 문의](#contact-microsoft-support) 하 여 추가 지원을 요청 하세요.

## <a name="sspr-usage"></a>SSPR 사용

SSPR를 사용 하 여 사용자 또는 사용자에 게 문제가 있는 경우 다음 문제 해결 시나리오와 해결 단계를 검토 합니다.

| Error | 해결 방법 |
| --- | --- |
| 암호 재설정에 디렉터리를 사용할 수 없습니다. | Azure Portal에서 **셀프 서비스 암호 재설정 사용** 구성을 *선택* 됨 또는 *모두* 로 변경 하 고 **저장** 을 선택 합니다. |
| 사용자에 게 Azure AD 라이선스가 할당 되어 있지 않습니다. | 이 문제는 원하는 사용자에 게 Azure AD 라이선스가 할당 되지 않은 경우에 발생할 수 있습니다. 문제의 관리자 계정에 라이선스를 할당 하려면 [라이선스 관련 문제를 할당, 확인 및 해결](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses)하는 단계를 수행 합니다. |
| 암호 재설정에 디렉터리를 사용할 수 있지만 사용자가 인증 정보를 누락했거나 형식이 잘못 되었습니다. | 사용자의 디렉터리에 있는 파일에 대 한 연락처 데이터가 올바르게 구성 되어 있는지 확인 합니다. 자세한 내용은 [Azure AD 셀프 서비스 암호 재설정에서 사용하는 데이터](howto-sspr-authenticationdata.md)를 참조하세요. |
| 암호 재설정에 디렉터리를 사용할 수 있지만 두 개의 검증 단계가 필요하도록 정책이 설정된 경우 사용자는 파일에 하나의 연락처 데이터만 유지하게 됩니다. | 사용자에 게 올바르게 구성 된 연락 방법이 두 개 이상 있는지 확인 합니다. 예로 휴대폰 번호 *와* 사무실 전화 번호를 둘 다 사용하는 경우를 예로 들 수 있습니다. |
| 암호 재설정에 디렉터리를 사용할 수 있고 사용자가 올바르게 구성되어 있지만 사용자와 연결할 수 없습니다. | 이것은 임시 서비스 오류가 발생 하거나 제대로 검색할 수 없는 잘못 된 연락처 데이터가 있는 경우에 발생할 수 있습니다. <br> <br> 사용자가 10 초 동안 기다리면 링크가 "다시 시도" 및 "관리자에 게 문의"에 표시 됩니다. 사용자가 "다시 시도"를 선택 하면 호출을 다시 시도 합니다. 사용자가 "관리자에 게 문의"를 선택 하는 경우 관리자에 게 해당 사용자 계정에 대해 암호 재설정을 수행 하도록 요청 하는 전자 메일 양식을 보냅니다. |
| 사용자는 암호 재설정 SMS 또는 전화 통화를 수신하지 않습니다 | 이것은 디렉터리에 형식이 잘못된 전화 번호가 포함되어 있기 때문일 수 있습니다. 전화 번호가 "+ 1 4251234567" 형식 인지 확인 합니다. <br> <br>디렉터리에 하나를 지정 하는 경우에도 암호 재설정은 확장을 지원 하지 않습니다. 호출을 수행 하기 전에 확장이 제거 됩니다. 내선 번호를 사용 하지 않고 내선 번호를 PBX (private branch exchange)의 전화 번호로 통합 합니다. |
| 사용자는 암호 재설정 전자 메일을 수신하지 못합니다. | 이 문제에 대한 가장 일반적인 원인은 스팸 필터에 의해 메시지가 거부된다는 점입니다. 전자 메일에 대한 스팸, 정크, 또는 삭제된 항목 폴더를 확인하십시오. <br> <br> 또한 사용자가 SSPR에 등록 된 대로 올바른 전자 메일 계정을 확인 해야 합니다. |
| 암호 재설정 정책을 설정 했지만 관리자 계정이 암호 재설정을 사용 하는 경우 해당 정책이 적용 되지 않습니다. | Microsoft에서는 관리자 암호 재설정 정책을 관리하고 제어하여 가장 높은 수준의 보안을 보장합니다. |
| 사용자는 하루에 너무 많이 암호를 재설정하려 할 수 없습니다. | 자동 제한 메커니즘을 사용 하 여 사용자가 짧은 기간 동안 여러 번 암호를 다시 설정 하지 못하도록 차단할 수 있습니다. 제한은 다음과 같은 시나리오에서 발생 합니다. <br><ul><li>사용자가 한 시간 동안 5회에 걸쳐 전화 번호의 유효성을 검사하려 합니다.</li><li>사용자가 한 시간 동안 5회에 걸쳐 보안 질문 게이트를 사용하려 합니다.</li><li>사용자가 한 시간 동안 5회에 걸쳐 동일한 사용자 계정에 대한 암호를 재설정하려 합니다.</li></ul>사용자가이 문제를 발견 한 경우 마지막 시도 후 24 시간 동안 기다려야 합니다. 사용자는 그 후에 암호를 재설정해야 합니다. |
| 사용자가 자신의 전화 번호의 유효성을 검사하는 경우 오류가 표시됩니다. | 입력 한 전화 번호가 파일의 전화 번호와 일치 하지 않는 경우이 오류가 발생 합니다. 사용자가 암호 재설정을 위해 전화 기반 방법을 사용하려고 할 때 영역 및 국가 코드를 포함하여 전체 전화 번호를 입력하는지 확인합니다. |
| 요청을 처리 하는 동안 오류가 발생 했습니다. | 일반적인 SSPR 등록 오류는 많은 문제로 인해 발생할 수 있지만 일반적으로 서비스 중단 또는 구성 문제로 인해 발생 합니다. SSPR 등록 프로세스를 다시 시도할 때이 일반 오류가 계속 표시 되 면 [Microsoft 지원에 문의](#contact-microsoft-support) 하 여 추가 지원을 요청 하세요. |
| 온-프레미스 정책 위반 | 암호가 온-프레미스 Active Directory 암호 정책을 충족 하지 않습니다. 사용자는 복잡성 또는 강도 요구 사항을 충족 하는 암호를 정의 해야 합니다. |
| 암호가 유사 정책에 맞지 않음 | 사용 된 암호는 [금지 된 암호 목록](./concept-password-ban-bad.md#how-are-passwords-evaluated) 에 표시 되며 사용할 수 없습니다. 사용자는 금지 된 암호 목록 정책을 만족 하거나 초과 하는 암호를 정의 해야 합니다. |

## <a name="sspr-errors-that-a-user-might-see"></a>사용자가 볼 수 있는 SSPR 오류

다음 오류 및 기술 세부 정보는 SSPR 프로세스의 일부로 사용자에 게 표시 될 수 있습니다. SSPR 기능이 계정에 대해 사용, 구성 또는 등록 되어야 하므로 오류는 자신을 해결할 수 없는 경우가 많습니다.

다음 정보를 사용 하 여 문제를 이해 하 고 Azure AD 테 넌 트 또는 개별 사용자 계정에서 문제를 해결 해야 합니다.

| Error | 세부 정보 | 기술 세부 정보 |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | 관리자가 조직에 대해 암호 재설정을 사용 하지 않도록 설정 했기 때문에 지금은 암호를 재설정할 수 없습니다. 이 문제 해결을 위해 수행할 수 있는 추가 작업이 없습니다. 관리자에게 문의하고 이 기능을 활성화하도록 요청하세요.<br /><br />자세한 내용은 [Azure AD 암호를 잊어버렸어요. 도와주세요!](../user-help/active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions)를 참조하세요. | SSPR_0009: 관리자에 의해 암호 재설정이 활성화되지 않은 것을 감지했습니다. 관리자에게 문의하고 조직에 대한 암호 재설정을 활성화하도록 요청하세요. |
| WritebackNotEnabled = 10 |관리자가 조직에 필요한 서비스를 사용 하도록 설정 하지 않았기 때문에 지금은 암호를 재설정할 수 없습니다. 이 문제 해결을 위해 수행할 수 있는 추가 작업이 없습니다. 관리자에 게 문의 하 고 조직의 구성을 확인 하도록 요청 하세요.<br /><br />이 필요한 서비스에 대한 자세한 내용은 [비밀번호 쓰기 저장 구성](./tutorial-enable-sspr-writeback.md)을 참조하세요. | SSPR_0010: 비밀번호 쓰기 저장이 활성화되지 않은 것을 감지했습니다. 관리자에게 문의하고 비밀번호 쓰기 저장을 활성화하도록 요청하세요. |
| SsprNotEnabledInUserPolicy = 11 | 관리자가 조직에 대 한 암호 재설정을 구성 하지 않았기 때문에 지금은 암호를 재설정할 수 없습니다. 이 문제 해결을 위해 수행할 수 있는 추가 작업이 없습니다. 관리자에게 문의하고 암호 재설정을 구성하도록 요청하세요.<br /><br />비밀번호 재설정 구성에 대한 자세한 내용은 [빠른 시작: Azure AD 셀프 서비스 암호 재설정](./tutorial-enable-sspr.md)을 참조하세요. | SSPR_0011: 조직에서 암호 재설정 정책을 정의하지 않았습니다. 관리자에게 문의하고 암호 재설정 정책을 정의하도록 요청하세요. |
| UserNotLicensed = 12 | 죄송 합니다. 조직에서 필요한 라이선스가 누락 되었기 때문에 지금은 암호를 재설정할 수 없습니다. 이 문제 해결을 위해 수행할 수 있는 추가 작업이 없습니다. 관리자에게 문의하고 라이선스 할당을 확인하도록 요청하세요.<br /><br />라이선스에 대한 자세한 내용은 [Azure AD 셀프 서비스 암호 재설정의 라이선스 요구 사항](./concept-sspr-licensing.md)을 참조하세요. | SSPR_0012: 조직에 암호 재설정을 수행하는 데 필요한 필수 라이선스가 없습니다. 관리자에게 문의하고 라이선스 할당을 검토하도록 요청하세요. |
| UserNotMemberOfScopedAccessGroup = 13 | 관리자가 암호 재설정을 사용 하도록 계정을 구성 하지 않았기 때문에 지금은 암호를 재설정할 수 없습니다. 이 문제 해결을 위해 수행할 수 있는 추가 작업이 없습니다. 관리자에게 문의하고 암호 재설정에 대한 계정을 구성하도록 요청하세요.<br /><br />암호 재설정에 대한 계정 구성에 대한 자세한 내용은 [사용자 암호 재설정 롤아웃](./howto-sspr-deployment.md)을 참조하세요. | SSPR_0013: 암호를 재설정할 수 있는 그룹의 구성원이 아닙니다. 관리자에게 문의하고 그룹에 추가되도록 요청하세요. |
| UserNotProperlyConfigured = 14 | 죄송 합니다. 계정에서 필요한 정보가 누락 되었기 때문에 지금은 암호를 재설정할 수 없습니다. 이 문제 해결을 위해 수행할 수 있는 추가 작업이 없습니다. 관리자에게 문의하고 암호를 다시 설정하도록 요청하세요. 계정에 다시 액세스할 수 잇게 되면 필요한 정보를 등록해야 합니다.<br /><br />정보를 등록하려면 [셀프 서비스 암호 재설정 등록](../user-help/active-directory-passwords-reset-register.md) 문서의 단계를 따르세요. | SSPR_0014: 암호를 재설정하기 위해 추가 보안 정보가 필요합니다. 계속하려면 관리자에게 문의하고 암호를 다시 설정하도록 요청하세요. 계정에 대한 액세스를 얻은 후 https://aka.ms/ssprsetup 에서 추가 보안 정보를 등록할 수 있습니다. 관리자는 [암호 재설정에 대한 인증 데이터 설정 및 읽기](howto-sspr-authenticationdata.md)의 단계를 따라 계정에 추가 보안 정보를 추가할 수 있습니다. |
| OnPremisesAdminActionRequired = 29 | 죄송 합니다. 조직의 암호 재설정 구성 문제 때문에 지금은 암호를 재설정할 수 없습니다. 이 문제 해결을 위해 수행할 수 있는 추가 작업이 없습니다. 관리자에게 문의하고 조사하도록 요청하세요. <br /><br />또는<br /><br />조직의 암호 재설정 구성 문제 때문에 지금은 암호를 재설정할 수 없습니다. 이 문제를 해결 하기 위해 수행할 수 있는 추가 작업은 없습니다. 관리자에게 문의하고 조사하도록 요청하세요.<br /><br />잠재적 문제에 대한 자세한 내용은 [비밀번호 쓰기 저장 문제 해결](troubleshoot-sspr-writeback.md)을 참조하세요. | SSPR_0029: 온-프레미스 구성의 오류로 인해 암호를 다시 설정할 수 없습니다. 관리자에게 문의하고 조사하도록 요청하세요. |
| OnPremisesConnectivityError = 30 | 죄송 합니다. 조직에 대 한 연결 문제 때문에 지금은 암호를 재설정할 수 없습니다. 지금 바로 수행할 작업이 없지만 나중에 다시 시도하면 문제를 해결할 수 있습니다. 문제가 계속되면 관리자에게 문의하고 조사하도록 요청하세요.<br /><br />연결 문제에 대해 알아보려면 [비밀번호 쓰기 저장 연결 문제 해결](troubleshoot-sspr-writeback.md)을 참조하세요. | SSPR_0030: 온-프레미스 환경과의 불안정한 연결로 인해 암호를 재설정할 수 없습니다. 관리자에게 문의하고 조사하도록 요청하세요.|

## <a name="azure-ad-forums"></a>Azure AD 포럼

Azure AD 및 셀프 서비스 암호 재설정에 대 한 일반적인 질문이 있는 경우 [Microsoft Q&Azure Active Directory에 대 한 질문 페이지](/answers/topics/azure-active-directory.html)에서 커뮤니티에 도움을 요청할 수 있습니다. 커뮤니티는 엔지니어, 제품 관리자, MVP 및 동료 IT 전문가들로 구성되어 있습니다.

## <a name="contact-microsoft-support"></a>Microsoft 지원에 문의

문제에 대한 답을 찾을 수 없는 경우 지원 팀에서 항상 추가 지원을 받을 수 있습니다.

제대로 지원하기 위해 사례를 시작할 때 가능한 많은 세부 정보를 제공하도록 요청합니다. 이러한 세부 정보는 다음과 같습니다.

* **오류에 대한 일반적인 설명**: 오류란 무엇인가요? 어떤 동작이 발견되었습니까? 오류를 재현하려면 어떻게 해야 합니까? 최대한 많은 세부 정보를 제공해주세요.
* **페이지**: 어떤 페이지에서 오류가 나타났나요? 가능하면 URL 및 페이지 스크린샷을 포함해주세요.
* **지원 코드**: 사용자가 오류를 확인했을 때 생성된 지원 코드는 무엇이었나요?
   * 이 코드를 찾으려면 오류를 재현한 후 화면 아래에서 **지원 코드** 링크를 선택하고 지원 엔지니어에게 결과로 표시된 GUID를 보내세요.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="지원 코드는 웹 브라우저 창의 오른쪽 아래에 있습니다.":::

  * 아래에서 지원 코드 없이 페이지에서 F12 키를 선택하고 SID 및 CID를 검색한 후 지원 엔지니어에게 이러한 두 개의 결과를 보냅니다.
* **날짜, 시간 및 표준 시간대**: 오류가 발생한 *표준 시간대와* 정확한 날짜 및 시간을 포함해주세요.
* **사용자 ID**: 어떤 사용자에게서 오류가 나타났나요? 예를 들면 ‘사용자\@contoso.com’입니다.
   * 페더레이션된 사용자입니까?
   * 통과 인증 사용자입니까?
   * 암호 해시 동기화 사용자인가요?
   * 클라우드 전용 사용자인가요?
* **라이선스**: 사용자에게 Azure AD 라이선스가 할당되었나요?
* **애플리케이션 이벤트 로그**: 비밀번호 쓰기 저장을 사용 중이고 온-프레미스 인프라에서 오류가 발생한 경우 Azure AD Connect 서버에서 애플리케이션 이벤트 로그의 복사본을 압축하여 보내주세요.

## <a name="next-steps"></a>다음 단계

SSPR에 대 한 자세한 내용은 [작동 방식: AZURE ad 셀프 서비스 암호 재설정](concept-sspr-howitworks.md) 또는 [azure ad에서 셀프 서비스 암호 다시 설정 쓰기 저장이 작동 하는 방식](concept-sspr-writeback.md)을 참조 하세요.
