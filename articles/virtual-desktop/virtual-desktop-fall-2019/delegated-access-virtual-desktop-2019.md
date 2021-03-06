---
title: Windows 가상 데스크톱 (클래식)에서 위임 된 액세스-Azure
description: 예를 포함 하 여 Windows 가상 데스크톱 (클래식) 배포에 대 한 관리 기능을 위임 하는 방법
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0152dc5102fd3f77418448234cab1234d25b97c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88008154"
---
# <a name="delegated-access-in-windows-virtual-desktop-classic"></a>Windows 가상 데스크톱 (클래식)의 위임 된 액세스

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 지원하지 않는 Windows Virtual Desktop(클래식)에 적용됩니다. Azure Resource Manager Windows Virtual Desktop 개체를 관리하려는 경우 [이 문서](../delegated-access-virtual-desktop.md)를 참조하세요.

Windows 가상 데스크톱에는 특정 사용자에 게 역할을 할당 하 여 허용 되는 액세스 권한을 정의할 수 있는 위임 된 액세스 모델이 있습니다. 역할 할당에는 보안 주체, 역할 정의 및 범위의 세 가지 구성 요소가 있습니다. Windows 가상 데스크톱 위임 된 액세스 모델은 Azure RBAC 모델을 기반으로 합니다. 특정 역할 할당 및 해당 구성 요소에 대해 자세히 알아보려면 [Azure 역할 기반 액세스 제어 개요](../../role-based-access-control/built-in-roles.md)를 참조 하세요.

Windows 가상 데스크톱 위임 된 액세스는 역할 할당의 각 요소에 대해 다음 값을 지원 합니다.

* 보안 주체
    * 사용자
    * 서비스 주체
* 역할 정의
    * 기본 제공 역할
* 범위
    * 테 넌 트 그룹
    * 테넌트
    * 호스트 풀
    * 앱 그룹

## <a name="built-in-roles"></a>기본 제공 역할

Windows 가상 데스크톱의 위임 된 액세스에는 사용자 및 서비스 사용자에 게 할당할 수 있는 몇 가지 기본 제공 역할 정의가 있습니다.

* RDS 소유자는 리소스에 대 한 액세스를 포함 하 여 모든 것을 관리할 수 있습니다.
* RDS 참가자는 모든 항목을 관리할 수 있지만 리소스에는 액세스할 수 없습니다.
* RDS Reader는 모든 항목을 볼 수 있지만 변경할 수는 없습니다.
* RDS 운영자는 진단 작업을 볼 수 있습니다.

## <a name="powershell-cmdlets-for-role-assignments"></a>역할 할당에 대 한 PowerShell cmdlet

다음 cmdlet을 실행 하 여 역할 할당을 만들고, 확인 하 고, 제거할 수 있습니다.

* **RdsRoleAssignment** 역할 할당 목록을 표시 합니다.
* **RdsRoleAssignment** 는 새 역할 할당을 만듭니다.
* **RdsRoleAssignment** 역할 할당을 삭제 합니다.

### <a name="accepted-parameters"></a>허용 되는 매개 변수

다음 매개 변수를 사용 하 여 기본 3 개의 cmdlet을 수정할 수 있습니다.

* **AadTenantId**: 서비스 사용자가 구성원 인 Azure Active Directory 테 넌 트 ID를 지정 합니다.
* **Appgroupname**: 원격 데스크톱 앱 그룹의 이름입니다.
* **진단**: 진단 범위를 나타냅니다. 는 **인프라** 또는 **테 넌 트** 매개 변수와 쌍을 이루어야 합니다.
* **HostPoolName**: 원격 데스크톱 호스트 풀의 이름입니다.
* **인프라**: 인프라 범위를 나타냅니다.
* **Roledefinitionname**: 사용자, 그룹 또는 앱에 할당 된 원격 데스크톱 서비스 역할 기반 액세스 제어 역할의 이름입니다. (예를 들어 원격 데스크톱 서비스 소유자, 원격 데스크톱 서비스 판독기 등)
* **ServerPrincipleName**: Azure Active Directory 응용 프로그램의 이름입니다.
* **SignInName**: 사용자의 전자 메일 주소 또는 사용자 계정 이름입니다.
* **Tenantname**: 원격 데스크톱 테 넌 트의 이름입니다.

## <a name="next-steps"></a>다음 단계

각 역할이 사용할 수 있는 PowerShell cmdlet의 전체 목록은 [powershell 참조](/powershell/windows-virtual-desktop/overview)를 참조 하세요.

Windows 가상 데스크톱 환경을 설정 하는 방법에 대 한 지침은 [Windows 가상 데스크톱 환경](environment-setup-2019.md)을 참조 하세요.
