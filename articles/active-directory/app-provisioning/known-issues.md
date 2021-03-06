---
title: Azure AD에서 응용 프로그램 프로 비전에 대 한 알려진 문제
description: Azure AD에서 자동화 된 응용 프로그램 프로 비전을 사용할 때 알려진 문제에 대해 알아봅니다.
author: kenwith
ms.author: kenwith
manager: daveba
services: active-directory
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/05/2021
ms.reviewer: arvinh
ms.openlocfilehash: 9eba671f6c824c8c88388f2b9d61512dfb1d122f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256646"
---
# <a name="known-issues-application-provisioning"></a>알려진 문제: 응용 프로그램 프로 비전
앱 프로비저닝 작업 시 알아야 할 알려진 문제 UserVoice의 응용 프로그램 프로 비전 서비스에 대 한 피드백을 제공할 수 있습니다. [AZURE AD 응용 프로그램 프로 비전 uservoice](https://aka.ms/appprovisioningfeaturerequest)를 참조 하세요. 서비스를 개선할 수 있도록 UserVoice를 면밀 하 게 감시 합니다. 

> [!NOTE]
> 이는 알려진 문제에 대 한 포괄적인 목록이 아닙니다. 나열 되지 않은 문제를 알고 있는 경우 페이지의 맨 아래에 피드백을 제공 합니다.

## <a name="authorization"></a>권한 부여 

**연결 테스트가 완료 된 후에는 저장할 수 없습니다.**

연결을 성공적으로 테스트할 수 있지만 저장할 수 없는 경우 자격 증명에 대해 허용 되는 저장소 제한을 초과 했습니다. 자세히 알아보려면 [관리자 자격 증명 저장 문제](./user-provisioning.md)를 참조 하세요.

**저장할 수 없음**

저장 하려면 테 넌 트 URL, 비밀 토큰 및 알림 전자 메일을 채워야 합니다. 둘 중 하나만 제공할 수는 없습니다. 

**프로 비전 모드를 수동으로 다시 변경할 수 없음**

처음으로 프로 비전을 구성한 후에는 프로 비전 모드가 수동에서 자동으로 전환 되었음을 알 수 있습니다. 다시 수동으로 변경할 수 없습니다. 그러나 UI를 통해 프로 비전을 해제할 수 있습니다. UI에서 프로 비전을 해제 하는 것은 드롭다운을 수동으로 설정 하는 것과 동일 합니다.  


## <a name="attribute-mappings"></a>특성 매핑 

**특성 SamAccountName 또는 userType은 원본 특성으로 사용할 수 없습니다.**

SamAccountName 및 userType 특성은 기본적으로 원본 특성으로 사용할 수 없습니다. 스키마를 확장 하 여 특성을 추가 합니다. 스키마를 확장 하 여 사용 가능한 원본 특성 목록에 특성을 추가할 수 있습니다. 자세한 내용은 [누락 된 원본 특성](user-provisioning-sync-attributes-for-mapping.md)을 참조 하세요. 

**스키마 확장에 대 한 원본 특성 dropdown이 없습니다.**

때로는 스키마에 대 한 확장이 UI의 원본 특성 드롭다운에서 누락 될 수 있습니다. 특성 매핑의 고급 설정으로 이동 하 여 특성을 수동으로 추가 합니다. 자세히 알아보려면 [특성 매핑 사용자 지정](customize-application-attributes.md)을 참조 하세요.

**Null 특성을 프로 비전 할 수 없습니다.**

Azure AD는 현재 null 특성을 프로 비전 할 수 없습니다. 사용자 개체의 특성이 null 이면 건너뜁니다. 

**특성 매핑 식의 최대 문자**

특성 매핑 식에는 최대 1만 문자를 사용할 수 있습니다. 

**지원 되지 않는 범위 지정 필터**

디렉터리 확장, appRoleAssignments, userType 및 accountExpires는 범위 지정 필터로 지원 되지 않습니다.


## <a name="service-issues"></a>서비스 문제 

**지원되지 않는 시나리오**

- 프로 비전 암호는 지원 되지 않습니다. 
- 중첩 그룹 프로 비전은 지원 되지 않습니다. 
- 테 넌 트의 크기 때문에 B2C 테 넌 트에 프로 비전이 지원 되지 않습니다.
- 모든 클라우드에서 프로 비전 앱을 모두 사용할 수 있는 것은 아닙니다. 예를 들어 정부 클라우드에서는 아직 Atlassian를 사용할 수 없습니다. 앱 개발자와 협력 하 여 앱을 모든 클라우드에 등록 합니다.

**내 OIDC 기반 응용 프로그램에서 자동 프로비저닝을 사용할 수 없음**

앱 등록을 만드는 경우 enterprise apps의 해당 서비스 주체는 자동 사용자 프로 비전을 사용 하도록 설정 되지 않습니다. 여러 조직에서 사용 하려는 경우 갤러리에 앱을 추가 하도록 요청 하거나 프로 비전을 위해 두 번째 비 갤러리 앱을 만들어야 합니다. 

**프로 비전 간격이 고정 되어 있습니다.**

프로 비전 주기 사이의 [시간은](./application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users) 현재 구성할 수 없습니다. 

**대상 앱에서 Azure AD로 이동 하지 않는 변경 내용**

앱 프로 비전 서비스는 외부 앱에서 변경한 내용을 인식 하지 못합니다. 따라서 롤백하는 동작이 수행 되지 않습니다. 앱 프로 비전 서비스는 Azure AD에서 변경 된 내용을 따릅니다. 

**모두 동기화에서 할당 되지 않은 동기화로 전환**

' 동기화 모두 '에서 ' 동기화 할당 됨 '으로 범위를 변경한 후에도 다시 시작을 수행 하 여 변경 내용이 적용 되는지 확인 하세요. UI에서 다시 시작할 수 있습니다.

**프로 비전 주기는 완료 될 때까지 계속 됩니다.**

프로 비전 `enabled = off` 을 설정 하거나 중지를 적중 하면 현재 프로 비전 주기가 완료 될 때까지 계속 실행 됩니다. 서비스는 다시 프로 비전을 다시 설정할 때까지 이후 사이클의 실행을 중지 합니다.

**그룹의 멤버가 프로 비전 되지 않았습니다.**

그룹이 범위 내에 있고 멤버가 범위를 벗어난 경우 그룹이 프로 비전 됩니다. 범위를 벗어난 사용자는 프로 비전 되지 않습니다. 멤버가 범위에 다시 들어오면 서비스에서 즉시 변경 내용을 검색 하지 않습니다. 프로 비전을 다시 시작 하면 문제가 해결 됩니다. 정기적으로 서비스를 다시 시작 하 여 모든 사용자가 적절히 프로 비전 되도록 하는 것이 좋습니다.  

**관리자가 프로 비전 되지 않음**

사용자와 해당 관리자가 둘 다 프로 비전 범위에 있는 경우 서비스에서 사용자를 프로 비전 한 다음 관리자를 업데이트 합니다. 그러나 사용자가 범위 내에 있고 관리자가 범위를 벗어난 경우 관리자가 참조 하지 않고 사용자를 프로 비전 합니다. 관리자가 범위에 들어오면 프로 비전을 다시 시작 하 고 서비스에서 모든 사용자를 다시 평가할 때까지 관리자 참조가 업데이트 되지 않습니다. 

## <a name="next-steps"></a>다음 단계
- [프로비저닝 작동 방법](how-provisioning-works.md)
