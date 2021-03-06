---
title: 패키지 관리
description: Azure Synapse Analytics에서 Apache Spark에 사용 되는 라이브러리를 추가 하 고 관리 하는 방법을 알아봅니다.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/01/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: e8ad6d072af6979eb8509068c1dcd239e7840950
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598017"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 Apache Spark에 대 한 라이브러리 관리
라이브러리는 프로그램 또는 프로젝트에 포함 시킬 수 있는 재사용 가능한 코드를 제공 합니다. 

여러 가지 이유로 서버를 사용 하지 않는 Apache Spark 풀 환경을 업데이트 해야 할 수 있습니다. 예를 들어 다음을 확인할 수 있습니다.
- 핵심 종속성 중 하나가 새 버전을 릴리스 했습니다.
- machine learning 모델을 학습 하거나 데이터를 준비 하기 위한 추가 패키지가 필요 합니다.
- 더 나은 패키지를 찾았지만 이전 패키지가 더 이상 필요 하지 않습니다.
- 팀이 Apache Spark 풀에서 사용할 수 있는 사용자 지정 패키지를 빌드 했습니다.

응용 프로그램에서 타사 또는 로컬로 빌드된 코드를 사용할 수 있도록 하려면 서버를 사용 하지 않는 Apache Spark 풀 또는 노트북 세션 중 하나에 라이브러리를 설치 하면 됩니다.
  
## <a name="default-installation"></a>기본 설치
Azure Synapse Analytics의 Apache Spark에는 완전 한 Anacondas 설치와 추가 라이브러리가 있습니다. 전체 라이브러리 목록은 [Apache Spark 버전 지원](apache-spark-version-support.md)에서 찾을 수 있습니다. 

Spark 인스턴스가 시작 되 면 이러한 라이브러리가 자동으로 포함 됩니다. 추가 패키지는 Spark 풀 수준 또는 세션 수준에서 추가할 수 있습니다.

## <a name="workspace-packages"></a>작업 영역 패키지
사용자 지정 응용 프로그램 또는 모델을 개발할 때 팀에서 휠 또는 jar 파일과 같은 다양 한 코드 아티팩트를 개발 하 여 코드를 패키지할 수 있습니다. 

Synapse에서 작업 영역 패키지는 사용자 지정 또는 개인 휠 또는 jar 파일이 될 수 있습니다. 이러한 패키지를 작업 영역에 업로드 하 고 나중에 특정 Spark 풀에 할당할 수 있습니다. 할당 되 면 이러한 작업 영역 패키지가 모든 Spark 풀 세션에 자동으로 설치 됩니다.

작업 영역 라이브러리를 관리 하는 방법에 대 한 자세한 내용을 보려면 다음 방법 가이드를 참조 하세요.

- [Python 작업 영역 패키지 (미리 보기): ](./apache-spark-manage-python-packages.md#install-wheel-files) Python 휠 파일을 작업 영역 패키지로 업로드 하 고 나중에 이러한 패키지를 서버를 사용 하지 않는 특정 Apache Spark 풀에 추가 합니다.
- [Scala/Java 작업 영역 패키지 (미리 보기): ](./apache-spark-manage-scala-packages.md#workspace-packages) Scala 및 Java jar 파일을 작업 영역 패키지로 업로드 하 고 나중에 이러한 패키지를 서버를 사용 하지 않는 특정 Apache Spark 풀에 추가 합니다.

## <a name="pool-packages"></a>풀 패키지
경우에 따라 지정 된 Apache Spark 풀에서 사용 되는 패키지 집합을 표준화할 수 있습니다. 이러한 표준화는 팀의 여러 사람이 동일한 패키지를 일반적으로 설치 하는 경우에 유용할 수 있습니다. 

Azure Synapse Analytics 풀 관리 기능을 사용 하 여 지정 된 서버를 사용 하지 않는 Apache Spark 풀에 설치할 기본 라이브러리 집합을 구성할 수 있습니다. 이러한 라이브러리는 [기본 런타임](./apache-spark-version-support.md)위에 설치 됩니다. 

현재는 풀 관리가 Python 에서만 지원 됩니다. Python의 경우 Synapse Spark 풀은 Conda를 사용 하 여 Python 패키지 종속성을 설치 하 고 관리 합니다. 풀 수준 라이브러리를 지정 하는 경우 이제 requirements.txt 또는 환경을 제공할 수 있습니다. yml. 이 환경 구성 파일은 spark 풀에서 Spark 인스턴스를 만들 때마다 사용 됩니다. 

이러한 기능에 대해 자세히 알아보려면 [Python 풀 관리](./apache-spark-manage-python-packages.md#pool-libraries)에 대 한 설명서를 참조 하세요.

> [!IMPORTANT]
> - 설치 하는 패키지가 크거나 설치 하는 데 시간이 오래 걸리는 경우 Spark 인스턴스 시작 시간에 영향을 줍니다.
> - PySpark, Python, Scala/Java, .NET 또는 Spark 버전을 변경 하는 것은 지원 되지 않습니다.
> - PyPI에서 패키지 설치는 DEP 사용 작업 영역 내에서 지원 되지 않습니다.

## <a name="session-scoped-packages"></a>세션 범위 패키지
대화형 데이터 분석 이나 기계 학습을 수행 하는 경우에는 종종 최신 패키지를 사용해 보거나 Apache Spark 풀에서 아직 사용할 수 없는 패키지가 필요할 수 있습니다. 이제 사용자는 풀 구성을 업데이트 하는 대신 세션 범위 패키지를 사용 하 여 세션 종속성을 추가, 관리 및 업데이트할 수 있습니다.

세션 범위 패키지를 사용 하면 사용자가 세션을 시작할 때 패키지 종속성을 정의할 수 있습니다. 세션 범위 패키지를 설치 하는 경우 현재 세션에만 지정 된 패키지에 대 한 액세스 권한이 있습니다. 따라서 이러한 세션 범위 패키지는 동일한 Apache Spark 풀을 사용 하는 다른 세션 또는 작업에 영향을 주지 않습니다. 또한 이러한 라이브러리는 기본 런타임 및 풀 수준 패키지 위에 설치 됩니다. 

세션 범위 패키지를 관리 하는 방법에 대 한 자세한 내용을 보려면 다음 방법 가이드를 참조 하세요.

- [Python 세션 패키지 (미리 보기):](./apache-spark-manage-python-packages.md) 세션을 시작할 때 인기 있는 리포지토리에서 추가 Python 패키지를 설치 하는 데 Conda 환경을 제공 합니다 *.* 
- [Scala/Java 세션 패키지: ](./apache-spark-manage-scala-packages.md) 세션을 시작할 때를 사용 하 여 설치할 jar 파일 목록을 제공 `%%configure` 합니다.

## <a name="next-steps"></a>다음 단계
- 기본 라이브러리 보기: [Apache Spark 버전 지원](apache-spark-version-support.md)
