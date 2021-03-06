---
title: Azure Policy 사용 준수
description: Azure Policy의 기본 제공 정책을 할당 하 여 Azure container registry의 준수 감사
ms.topic: article
ms.date: 03/01/2021
ms.openlocfilehash: 0fed0c4132043e1eaed7e634e1f45b27f7c6e933
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014301"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Azure Policy를 사용 하 여 Azure container registry의 준수 감사

[Azure Policy](../governance/policy/overview.md) 는 정책을 만들고 할당 하 고 관리 하는 데 사용 하는 Azure의 서비스입니다. 정책은 리소스에 대해 다양한 규칙과 효과를 적용하여 리소스가 회사 표준 및 서비스 수준 약정을 준수하도록 유지합니다.

이 문서에서는 Azure Container Registry에 대 한 기본 제공 정책을 소개 합니다. 이러한 정책을 사용 하 여 새로운 및 기존 레지스트리를 준수 하도록 감사 합니다.

Azure Policy 사용에 대 한 요금은 없습니다.

## <a name="built-in-policy-definitions"></a>기본 제공 정책 정의

다음 기본 제공 정책 정의는 Azure Container Registry에만 적용 됩니다.

[!INCLUDE [azure-policy-reference-rp-containerreg](../../includes/policy/reference/byrp/microsoft.containerregistry.md)]

## <a name="assign-policies"></a>정책 할당

* [Azure Portal](../governance/policy/assign-policy-portal.md), [Azure CLI](../governance/policy/assign-policy-azurecli.md), [리소스 관리자 템플릿](../governance/policy/assign-policy-template.md)또는 Azure Policy sdk를 사용 하 여 정책을 할당 합니다.
* 리소스 그룹, 구독 또는 [Azure 관리 그룹](../governance/management-groups/overview.md)에 대 한 정책 할당 범위를 지정 합니다. 컨테이너 레지스트리 정책 할당은 범위 내의 기존 및 새 컨테이너 레지스트리에 적용 됩니다.
* 언제 든 지 [정책 적용](../governance/policy/concepts/assignment-structure.md#enforcement-mode) 을 사용 하거나 사용 하지 않도록 설정 합니다.

> [!NOTE]
> 정책을 할당 하거나 업데이트 한 후에는 할당을 정의 된 범위의 리소스에 적용 하는 데 약간의 시간이 걸립니다. [정책 평가 트리거에](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)대 한 정보를 참조 하세요.

## <a name="review-policy-compliance"></a>정책 준수 검토

Azure Portal, Azure 명령줄 도구 또는 Azure Policy Sdk를 사용 하 여 정책 할당에 의해 생성 된 호환성 정보에 액세스 합니다. 자세한 내용은 [Azure 리소스의 준수 데이터 가져오기](../governance/policy/how-to/get-compliance-data.md)를 참조 하세요.

리소스가 규정 비준수인 경우 여러 가지 원인이 있을 수 있습니다. 이유를 확인 하거나 변경 내용을 확인 하려면 [비준수 확인](../governance/policy/how-to/determine-non-compliance.md)을 참조 하세요.

### <a name="policy-compliance-in-the-portal"></a>포털의 정책 준수:

1. **모든 서비스** 를 선택 하 고 **정책을** 검색 합니다.
1. **준수** 를 선택 합니다.
1. 필터를 사용 하 여 준수 상태를 제한 하거나 정책을 검색 합니다.

    ![포털의 정책 준수](./media/container-registry-azure-policy/azure-policy-compliance.png)
    
1. 집계 준수 세부 정보 및 이벤트를 검토 하려면 정책을 선택 하세요. 필요한 경우 리소스 준수를 위해 특정 레지스트리를 선택 합니다.

### <a name="policy-compliance-in-the-azure-cli"></a>Azure CLI에서 정책 준수

Azure CLI를 사용 하 여 준수 데이터를 가져올 수도 있습니다. 예를 들어 CLI에서 [az policy 할당 list](/cli/azure/policy/assignment#az-policy-assignment-list) 명령을 사용 하 여 적용 되는 Azure Container Registry 정책의 정책 id를 가져옵니다.

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

샘플 출력:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

그런 다음 [az policy state list](/cli/azure/policy/state#az-policy-state-list) 를 실행 하 여 특정 정책 ID의 모든 리소스에 대해 JSON 형식 준수 상태를 반환 합니다.

```azurecli
az policy state list \
  --resource <policyID>
```

또는 [az policy state list](/cli/azure/policy/state#az-policy-state-list) 를 실행 하 여 *myregistry* 와 같은 특정 레지스트리 리소스의 JSON 형식 준수 상태를 반환 합니다.

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>다음 단계

* Azure Policy [정의](../governance/policy/concepts/definition-structure.md) 및 [효과](../governance/policy/concepts/effects.md)에 대해 자세히 알아보세요.

* [사용자 지정 정책 정의](../governance/policy/tutorials/create-custom-policy-definition.md)를 만듭니다.

* Azure의 [거 버 넌 스 기능](../governance/index.yml) 에 대해 자세히 알아보세요.
