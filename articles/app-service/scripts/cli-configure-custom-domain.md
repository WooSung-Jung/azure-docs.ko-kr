---
title: 'CLI: 사용자 지정 도메인을 앱에 매핑'
description: Azure CLI를 사용하여 App Service 앱의 배포 및 관리를 자동화하는 방법을 알아봅니다. 이 샘플에서는 사용자 지정 도메인을 앱에 매핑하는 방법을 보여줍니다.
tags: azure-service-management
ms.assetid: 5ac4a680-cc73-4578-bcd6-8668c08802c2
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 3bf9e3ec9dbb042b85e4aa5ff2c65bc38125ce23
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782534"
---
# <a name="map-a-custom-domain-to-an-app-service-app-using-cli"></a>CLI를 사용하여 App Service 앱에 사용자 지정 도메인 매핑

이 샘플 스크립트는 관련된 리소스를 사용하여 App Service에서 앱을 만든 다음, 여기에 `www.<yourdomain>`을 매핑합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 Azure CLI 버전 2.0 이상이 필요합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Map a custom domain to an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | App Service 계획을 만듭니다. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | App Service 앱을 만듭니다. |
| [`az webapp config hostname add`](/cli/azure/webapp/config/hostname#az_webapp_config_hostnam_eadd) | App Service 앱에 사용자 지정 도메인을 매핑합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 App Service CLI 스크립트 샘플은 [Azure App Service 설명서](../samples-cli.md)에서 확인할 수 있습니다.
