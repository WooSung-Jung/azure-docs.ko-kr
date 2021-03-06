---
title: Azure Cosmos DB용 Azure Synapse Link 지원 기능
description: Azure Cosmos DB용 Azure Synapse Link에서 지원하는 현재 작업 목록 이해
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 03/02/2021
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: cdc9f344e108fc58399f9bcb2a9f02a4659ecfe1
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627679"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-supported-features"></a>Azure Cosmos DB용 Azure Synapse Link 지원 기능

이 문서에서는 Azure Cosmos DB용 Azure Synapse Link에서 현재 지원되는 기능을 설명합니다.

## <a name="azure-synapse-support"></a>Azure Synapse 지원

Azure Cosmos DB에는 다음과 같은 두 가지 유형의 컨테이너가 있습니다.
* HTAP 컨테이너 - Synapse Link가 활성화된 컨테이너입니다. 이 컨테이너에는 트랜잭션 저장소와 분석 저장소가 모두 있습니다. 
* OLTP 컨테이너-링크가 사용 하도록 설정 되지 않은 컨테이너입니다. 이 컨테이너에는 트랜잭션 저장소와 분석 저장소만 있습니다.

> [!IMPORTANT]
> Azure Cosmos DB에 대 한 Azure Synapse 링크는 현재 관리 되는 가상 네트워크를 사용 하도록 설정 되지 않은 Synapse 작업 영역에서 지원 됩니다. 

Synapse 링크를 사용 하지 않고 Azure Cosmos DB 컨테이너에 연결할 수 있습니다. 이 시나리오에서는 트랜잭션 저장소에 대 한 읽기/쓰기만 가능 합니다. Azure Cosmos DB에 대 한 Synapse 링크 내에서 현재 지원 되는 기능 목록은 다음과 같습니다. 

| 범주              | 설명 |[Apache Spark 풀](../sql/on-demand-workspace-overview.md) | [서버리스 SQL 풀](../sql/on-demand-workspace-overview.md) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- |
| **런타임 지원** |Azure Cosmos DB에 액세스 하도록 지원 되는 Azure Synapse 런타임| ✓ | ✓ |
| **Azure Cosmos DB API 지원** | 지원 되는 Azure Cosmos DB API 종류 | SQL / MongoDB | SQL / MongoDB |
| **Object**  |Azure Cosmos DB 컨테이너를 직접 가리키면서 만들 수 있는 테이블과 같은 개체| 데이터 프레임, View, Table | 보기 |
| **읽기**    | 읽을 수 있는 Azure Cosmos DB 컨테이너의 유형입니다. | OLTP / HTAP | HTAP  |
| **쓰기**   | Azure Synapse 런타임을 사용 하 여 Azure Cosmos DB 컨테이너에 데이터를 쓸 수 있습니다. | 예 | 예 |

* Spark에서 Azure Cosmos DB 컨테이너에 데이터를 작성 하는 경우이 프로세스는 Azure Cosmos DB의 트랜잭션 저장소를 통해 수행 됩니다. 요청 단위를 사용 하 여 Azure Cosmos DB의 트랜잭션 성능에 영향을 줍니다.
* 외부 테이블을 통한 전용 SQL 풀 통합은 현재 지원 되지 않습니다.
 
## <a name="supported-code-generated-actions-for-spark"></a>Spark에 대해 지원되는 코드 생성 작업

| 제스처              | 설명 |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **DataFrame에 로드** |Spark DataFrame에 데이터 로드 및 읽기 |✓| ✓ |
| **Spark 테이블 만들기** |Azure Cosmos DB 컨테이너를 가리키는 테이블 만들기|✓| ✓ |
| **컨테이너에 DataFrame 쓰기** |컨테이너에 데이터 쓰기|✓| ✓ |
| **컨테이너에서 스트리밍 DataFrame 로드** |Azure Cosmos DB 변경 피드를 사용하여 데이터 스트리밍|✓| ✓ |
| **컨테이너에 스트리밍 DataFrame 쓰기** |Azure Cosmos DB 변경 피드를 사용하여 데이터 스트리밍|✓| ✓ |

## <a name="supported-code-generated-actions-for-serverless-sql-pool"></a>서버를 사용 하지 않는 SQL 풀에 대해 지원 되는 코드 생성 작업

| 제스처              | 설명 |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **데이터 탐색** |친숙 한 T-sql 구문 및 자동 스키마 유추를 사용 하 여 컨테이너에서 데이터 탐색|X| ✓ |
| **뷰 만들기 및 BI 보고서 빌드** |SQL 보기를 만들어 서버를 사용 하지 않는 SQL 풀을 통해 BI 용 컨테이너에 직접 액세스할 수 있습니다. |X| ✓ |
| **Cosmos DB 데이터와 함께 서로 다른 데이터 원본 조인** | CETAS를 사용 하 여 Azure Blob Storage 또는 Azure Data Lake Storage의 데이터와 함께 Cosmos DB 컨테이너의 데이터를 읽은 쿼리 결과를 저장 합니다. |X| ✓ |

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB용 Synapse Link에 연결](../quickstart-connect-synapse-link-cosmos-db.md)하는 방법을 참조하세요.
* [Spark를 사용 하 여 Cosmos DB 분석 저장소를 쿼리 하는 방법 알아보기](how-to-query-analytical-store-spark.md)