---
title: Azure CLI를 사용 하 여 Azure Database for MySQL 유연한 서버에서 읽기 복제본을 관리 합니다.
description: Azure CLI를 사용 하 Azure Database for MySQL 유연한 서버에서 읽기 복제본을 설정 하 고 관리 하는 방법을 알아봅니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/23/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: bc95cd3ab471826538a551687c38d1422e4b7163
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108658"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Azure CLI를 사용 하 Azure Database for MySQL 유연한 서버에서 읽기 복제본을 만들고 관리 하는 방법

> [!IMPORTANT]
> Azure Database for MySQL-유연한 서버에서 복제본 읽기는 미리 보기 상태입니다.

이 문서에서는 Azure CLI를 사용 하 여 Azure Database for MySQL 유연한 서버에서 읽기 복제본을 만들고 관리 하는 방법에 대해 설명 합니다. 읽기 복제본에 대한 자세한 내용은 [개요](concepts-read-replicas.md)를 참조하세요.

> [!Note]
> 고가용성 지원 서버에서는 복제본이 지원 되지 않습니다. 

## <a name="azure-cli"></a>Azure CLI
Azure CLI를 사용 하 여 읽기 복제본을 만들고 관리할 수 있습니다.

### <a name="prerequisites"></a>사전 요구 사항

- [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)
- 원본 서버로 사용 되는 [Azure Database for MySQL 유연한 서버](quickstart-create-server-cli.md) 입니다.

### <a name="create-a-read-replica"></a>읽기 복제본 만들기

> [!IMPORTANT]
> 기존 복제본이 없는 원본에 대 한 복제본을 만드는 경우 원본 복제본이 먼저 다시 시작 되어 복제를 위한 준비가 됩니다. 이를 고려하고 사용량이 적은 기간 동안 이러한 작업을 수행합니다.

다음 명령을 사용하여 읽기 복제본 서버를 만들 수 있습니다.

```azurecli-interactive
az mysql flexible-server replica create --replica-name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
``` 

> [!NOTE]
> 읽기 복제본은 원본과 동일한 서버 구성으로 만들어집니다. 복제본이 생성된 후에 복제본 서버 구성을 변경할 수 있습니다. 복제 서버는 항상 원본 서버와 동일한 리소스 그룹 및 동일한 위치에 생성 됩니다. 다른 리소스 그룹 또는 다른 구독에 복제본 서버를 만들려면 복제본 서버를 만든 후에 [이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)할 수 있습니다. 복제본이 원본을 사용할 수 있도록 복제본 서버 구성을 원본과 같거나 큰 값으로 유지 하는 것이 좋습니다.


### <a name="list-replicas-for-a-source-server"></a>원본 서버에 대 한 복제본 나열

지정 된 원본 서버에 대 한 모든 복제본을 보려면 다음 명령을 실행 합니다. 

```azurecli-interactive
az mysql flexible-server replica list --server-name mydemoserver --resource-group myresourcegroup
```

### <a name="stop-replication-to-a-replica-server"></a>복제본 서버로의 복제 중지

> [!IMPORTANT]
> 서버로의 복제는 중지하고 나면 취소할 수 없습니다. 원본 및 복제본 간에 복제가 중지 된 후에는 실행 취소할 수 없습니다. 복제를 중지하고 나면 복제본 서버는 독립 실행형 서버가 되어 읽기와 쓰기를 모두 지원합니다. 이 서버를 다시 복제본으로 설정할 수는 없습니다.

다음 명령을 사용하여 읽기 복제본 서버에 대한 복제를 중지할 수 있습니다.

```azurecli-interactive
az mysql flexible-server replica stop-replication --replica-name mydemoreplicaserver --resource-group myresourcegroup
```

### <a name="delete-a-replica-server"></a>복제본 서버 삭제

읽기 복제본 서버 삭제는 **[az mysql server delete](/cli/azure/mysql/server)** 명령을 실행하여 수행할 수 있습니다.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>원본 서버 삭제

> [!IMPORTANT]
> 원본 서버를 삭제하면 모든 복제본 서버에 대한 복제가 중지되며 원본 서버 자체도 삭제됩니다. 그러면 복제본 서버는 읽기와 쓰기를 모두 지원하는 독립 실행형 서버로 설정됩니다.

원본 서버를 삭제 하려면 **[az mysql 신축 서버 삭제](/cli/azure/mysql/flexible-server)** 명령을 실행할 수 있습니다.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>다음 단계

- [읽기 복제본](concepts-read-replicas.md)에 대해 자세히 알아보기