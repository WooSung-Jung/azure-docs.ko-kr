---
title: Azure AD Domain Services에서 일치 하지 않는 디렉터리 오류 수정 | Microsoft Docs
description: 일치 하지 않는 디렉터리 오류가 무엇 임을 알아보고이를 해결 하는 방법에 대해 알아보세요 Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: ee8174114f1b892210e8ee9173ce0eb1d09c7e31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619303"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-active-directory-domain-services-managed-domains"></a>기존 Azure Active Directory Domain Services 관리 되는 도메인에 대해 일치 하지 않는 디렉터리 오류 해결

Azure Active Directory Domain Services (Azure AD DS) 관리 되는 도메인에 일치 하지 않는 테 넌 트 오류가 표시 되는 경우 해결 될 때까지 관리 되는 도메인을 관리할 수 없습니다. 이 오류는 기본 Azure 가상 네트워크를 다른 Azure AD 디렉터리로 이동 하는 경우에 발생 합니다.

이 문서에서는 오류가 발생 하는 이유와 문제 해결 방법에 대해 설명 합니다.

## <a name="what-causes-this-error"></a>이 오류의 원인은 무엇 인가요?

Azure AD DS 관리 되는 도메인 및 가상 네트워크가 서로 다른 두 Azure AD 테 넌 트에 속할 경우 디렉터리 불일치 오류가 발생 합니다. 예를 들어 Contoso의 Azure AD 테 넌 트에서 실행 되는 *aaddscontoso.com* 라는 관리 되는 도메인이 있을 수 있습니다. 그러나 관리 되는 도메인에 대 한 Azure 가상 네트워크는 Fabrikam Azure AD 테 넌 트의 일부입니다.

Azure RBAC (역할 기반 액세스 제어)는 리소스에 대 한 액세스를 제한 하는 데 사용 됩니다. Azure AD 테 넌 트에서 Azure AD DS를 사용 하도록 설정 하면 자격 증명 해시가 관리 되는 도메인에 동기화 됩니다. 이 작업을 수행 하려면 Azure AD 디렉터리의 테 넌 트 관리자 여야 하며 자격 증명에 대 한 액세스를 제어 해야 합니다.

Azure 가상 네트워크에 리소스를 배포 하 고 트래픽을 제어 하려면 관리 되는 도메인을 배포 하는 가상 네트워크에 대 한 관리 권한이 있어야 합니다.

Azure RBAC가 일관 되 게 작동 하 고 Azure AD DS에서 사용 하는 모든 리소스에 안전 하 게 액세스할 수 있도록 관리 되는 도메인과 가상 네트워크는 동일한 Azure AD 테 넌 트에 속해야 합니다.

배포에 적용 되는 규칙은 다음과 같습니다.

- Azure AD 디렉터리에 여러 Azure 구독이 있을 수 있습니다.
- 한 Azure 구독에는 가상 네트워크와 같은 여러 리소스가 있을 수 있습니다.
- 단일 관리 되는 도메인은 Azure AD 디렉터리에 대해 사용 하도록 설정 됩니다.
- 동일한 Azure AD 테 넌 트 내에서 Azure 구독에 속한 가상 네트워크에서 관리 되는 도메인을 사용 하도록 설정할 수 있습니다.

### <a name="valid-configuration"></a>유효한 구성

다음 배포 시나리오에서 contoso 관리 되는 도메인은 Contoso Azure AD 테 넌 트에서 사용 하도록 설정 됩니다. 관리 되는 도메인은 Contoso Azure AD 테 넌 트가 소유 하는 Azure 구독에 속한 가상 네트워크에 배포 됩니다.

관리 되는 도메인과 가상 네트워크는 모두 동일한 Azure AD 테 넌 트에 속합니다. 이 예제 구성은 유효 하 고 완벽 하 게 지원 됩니다.

![동일한 Azure AD 테 넌 트의 관리 되는 도메인 및 가상 네트워크 부분을 사용 하는 유효한 Azure AD DS 테 넌 트 구성](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>테넌트 구성 불일치

이 예제 배포 시나리오에서 contoso 관리 되는 도메인은 Contoso Azure AD 테 넌 트에서 사용 하도록 설정 됩니다. 그러나 관리 되는 도메인은 Fabrikam Azure AD 테 넌 트가 소유 하는 Azure 구독에 속한 가상 네트워크에 배포 됩니다.

관리 되는 도메인과 가상 네트워크는 서로 다른 두 Azure AD 테 넌 트에 속합니다. 이 예제 구성은 일치 하지 않는 테 넌 트 이며 지원 되지 않습니다. 가상 네트워크는 관리 되는 도메인과 동일한 Azure AD 테 넌 트로 이동 해야 합니다.

![테넌트 구성 불일치](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>일치 하지 않는 테 넌 트 오류 해결

다음 두 옵션은 디렉터리 불일치 오류를 해결 합니다.

* 먼저 기존 Azure AD 디렉터리에서 [관리 되는 도메인을 삭제](delete-aadds.md) 합니다. 그런 다음 사용 하려는 가상 네트워크와 동일한 Azure AD 디렉터리에 [대체 관리 되는 도메인을 만듭니다](tutorial-create-instance.md) . 준비가 되 면 삭제 된 도메인에 이전에 조인 된 모든 컴퓨터를 다시 만든 관리 되는 도메인에 가입 시킵니다.
* 가상 네트워크가 포함 된 [azure 구독](../cost-management-billing/manage/billing-subscription-transfer.md) 을 관리 되는 도메인과 동일한 azure AD 디렉터리로 이동 합니다.

## <a name="next-steps"></a>다음 단계

Azure AD DS 문제 해결에 대 한 자세한 내용은 [문제 해결 가이드](troubleshoot.md)를 참조 하세요.
