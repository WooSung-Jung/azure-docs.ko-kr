---
title: Azure HDInsight의 사용자 지정 Apache Ambari 데이터베이스
description: 사용자 지정 Apache Ambari 데이터베이스를 사용 하 여 HDInsight 클러스터를 만드는 방법에 대해 알아봅니다.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: fe38ddc594060c78a2d26e9b25476e38736b4cf7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946057"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>사용자 지정 Ambari DB를 사용 하 여 HDInsight 클러스터 설정

Apache Ambari는 Apache Hadoop 클러스터의 관리 및 모니터링을 간소화 합니다. Ambari는 사용 하기 쉬운 웹 UI 및 REST API을 제공 합니다. Ambari는 HDInsight 클러스터에 포함되어 있으며 클러스터를 모니터링하고 구성을 변경하는데 사용됩니다.

[Hdinsight에서 클러스터를 설정](hdinsight-hadoop-provision-linux-clusters.md)하는 것과 같은 다른 문서에 설명 된 대로 정상적인 클러스터 만들기에서 Ambari는 hdinsight에서 관리 되 고 사용자가 액세스할 수 없는 [S0 Azure SQL Database](../azure-sql/database/resource-limits-dtu-single-databases.md#standard-service-tier) 에 배포 됩니다.

사용자 지정 Ambari DB 기능을 사용 하면 관리 하는 외부 데이터베이스에 새 클러스터를 배포 하 고 Ambari를 설정할 수 있습니다. 배포는 Azure Resource Manager 템플릿을 사용 하 여 수행 됩니다. 이 기능에는 다음과 같은 이점이 있습니다.

- 사용자 지정-데이터베이스의 크기 및 처리 용량을 선택 합니다. 많은 클러스터에서 많은 작업을 처리 하는 경우 사양이 낮은 Ambari 데이터베이스는 관리 작업에 병목 상태가 될 수 있습니다.
- 유연성-요구 사항에 맞게 데이터베이스를 확장할 수 있습니다.
- 제어-조직 요구 사항에 맞는 방식으로 데이터베이스에 대 한 백업 및 보안을 관리할 수 있습니다.

이 문서의 나머지 부분에서는 다음 사항에 대해 설명 합니다.

- 사용자 지정 Ambari DB 기능을 사용 하기 위한 요구 사항
- Apache Ambari에 대 한 고유한 외부 데이터베이스를 사용 하 여 HDInsight 클러스터를 프로 비전 하는 데 필요한 단계

## <a name="custom-ambari-db-requirements"></a>사용자 지정 Ambari DB 요구 사항

모든 클러스터 유형 및 버전을 사용 하 여 사용자 지정 Ambari DB를 배포할 수 있습니다. 여러 클러스터에서 동일한 Ambari DB를 사용할 수 없습니다.

사용자 지정 Ambari DB에는 다음과 같은 요구 사항이 있습니다.

- 데이터베이스 이름에는 하이픈 또는 공백을 포함할 수 없습니다.
- 기존 Azure SQL DB 서버 및 데이터베이스가 있어야 합니다.
- Ambari 설치를 위해 제공 하는 데이터베이스는 비어 있어야 합니다. 기본 dbo 스키마에는 테이블이 없어야 합니다.
- 데이터베이스에 연결 하는 데 사용 되는 사용자에 게는 데이터베이스에 대 한 SELECT, CREATE TABLE 및 INSERT 권한이 있어야 합니다.
- Ambari를 호스트할 서버에서 [Azure 서비스에](../azure-sql/database/vnet-service-endpoint-rule-overview.md#azure-portal-steps) 대 한 액세스를 허용 하는 옵션을 설정 합니다.
- HDInsight 서비스의 관리 IP 주소는 방화벽 규칙에서 허용 해야 합니다. 서버 수준 방화벽 규칙에 추가 해야 하는 IP 주소 목록은 [HDInsight 관리 ip 주소](hdinsight-management-ip-addresses.md) 를 참조 하세요.

외부 데이터베이스에서 Apache Ambari DB를 호스트 하는 경우 다음 사항에 주의 하십시오.

- Ambari를 보유 하는 Azure SQL DB의 추가 비용을 담당 하 게 됩니다.
- 사용자 지정 Ambari DB를 주기적으로 백업 합니다. Azure SQL Database는 자동으로 백업을 생성 하지만 백업 보존 시간 프레임은 다릅니다. 자세한 내용은 [자동 SQL 데이터베이스 백업에 대해 알아보기](../azure-sql/database/automated-backups-overview.md)를 참조하세요.

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>사용자 지정 Ambari DB를 사용 하 여 클러스터 배포

사용자 고유의 외부 Ambari 데이터베이스를 사용 하는 HDInsight 클러스터를 만들려면 [사용자 지정 AMBARI DB 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db)을 사용 합니다.

에서 매개 변수를 편집 `azuredeploy.parameters.json` 하 여 새 클러스터에 대 한 정보와 Ambari을 보유할 데이터베이스를 지정 합니다.

Azure CLI를 사용 하 여 배포를 시작할 수 있습니다. 을 `<RESOURCEGROUPNAME>` 클러스터를 배포 하려는 리소스 그룹으로 바꿉니다.

```azurecli
az deployment group create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="database-sizing"></a>데이터베이스 크기 조정

다음 표에서는 HDInsight 클러스터의 크기에 따라 선택할 수 있는 Azure SQL DB 계층에 대 한 지침을 제공 합니다.

| 작업자 노드 수 | 필수 DB 계층 |
|---|---|
| <= 4 | S0 |
| >4 && <= 8 | S1 |
| >8 && <= 16 | S2 |
| >16 && <= 32 | S3 |
| >32 && <= 64 | S4 |
| >64 && <= 128 | P2 |
| >128 | 지원에 문의 |

## <a name="next-steps"></a>다음 단계

- [Azure HDInsight에서 외부 메타데이터 저장소 사용](hdinsight-use-external-metadata-stores.md)
