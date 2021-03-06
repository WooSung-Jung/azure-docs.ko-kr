---
title: Azure Automation 업데이트 관리에 대 한 경고를 만드는 방법
description: 이 문서에서는 업데이트 평가 또는 배포 상태에 대해 알리도록 Azure alerts를 구성 하는 방법을 설명 합니다.
services: automation
ms.subservice: update-management
ms.date: 03/15/2021
ms.topic: conceptual
ms.openlocfilehash: 224a7b5457a099fd763ac657349fc5497824ab76
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601434"
---
# <a name="how-to-create-alerts-for-update-management"></a>업데이트 관리에 대 한 경고를 만드는 방법

Azure의 경고는 runbook 작업, 서비스 상태 문제 또는 Automation 계정과 관련 된 기타 시나리오의 결과를 사전에 알려 줍니다. Azure Automation에는 미리 구성 된 경고 규칙이 포함 되지 않지만 생성 되는 데이터를 기반으로 사용자 고유의 사용자를 만들 수 있습니다. 이 문서에서는 업데이트 관리에 포함 된 메트릭을 사용 하 여 경고 규칙을 만드는 방법에 대 한 지침을 제공 합니다.

## <a name="available-metrics"></a>사용 가능한 메트릭

Azure Automation 수집 되 고 Azure Monitor에 전달 되는 업데이트 관리와 관련 된 두 개의 고유한 플랫폼 메트릭을 만듭니다. 이러한 메트릭은 [메트릭 경고 규칙](../../azure-monitor/alerts/alerts-metric.md)을 사용 하 여 [메트릭 탐색기](../../azure-monitor/essentials/metrics-charts.md) 및 경고를 사용 하 여 분석에 사용할 수 있습니다.

내보내는 두 메트릭은 다음과 같습니다.

* 총 업데이트 배포 머신 실행
* 총 업데이트 배포 실행

경고에 사용 되는 경우이 두 메트릭은 추가 정보를 포함 하는 차원을 지원 하 여 특정 업데이트 배포 세부 정보에 대 한 경고 범위를 지정 합니다. 다음 표에서는 경고를 구성할 때 사용할 수 있는 메트릭 및 차원에 대 한 세부 정보를 보여 줍니다.

|신호 이름|차원|Description
|---|---|---|
|`Total Update Deployment Runs`|- 업데이트 배포 이름<br>- 상태 | 업데이트 배포의 전체 상태를 알립니다.|
|`Total Update Deployment Machine Runs`|- 업데이트 배포 이름</br>- 상태</br>- 대상 컴퓨터</br>- 업데이트 배포 실행 ID    |특정 머신을 대상으로 하는 업데이트 배포의 상태를 알립니다.|

## <a name="create-alert"></a>경고 만들기

업데이트 배포 상태를 확인할 수 있도록 경고를 설정 하려면 다음 단계를 수행 합니다. Azure alerts를 처음 접하는 경우 [Azure alerts 개요](../../azure-monitor/alerts/alerts-overview.md)를 참조 하세요.

1. Automation 계정에서 **모니터링** 아래에 있는 **경고** 를 선택 하 고 **새 경고 규칙** 을 선택 합니다.

1. **경고 규칙 만들기** 페이지에서 Automation 계정이 이미 리소스로 선택 되어 있습니다. 변경 하려면 **리소스 편집** 을 선택 합니다.

1. 리소스 선택 페이지의 **리소스 유형별 필터** 드롭다운 목록에서 **Automation 계정** 을 선택 합니다.

1. 사용 하려는 Automation 계정을 선택 하 고 **완료** 를 선택 합니다.

1. **조건 추가** 를 선택 하 여 요구 사항에 적합 한 신호를 선택 합니다.

1. 차원의 경우 목록에서 유효한 값을 선택합니다. 원하는 값이 목록에 없는 경우 **\+** 차원 옆을 선택 하 고 사용자 지정 이름을 입력 합니다. 그런 다음, 찾을 값을 선택합니다. 차원에 대 한 모든 값을 선택 하려면 **선택 \*** 단추를 선택 합니다. 차원 값을 선택하지 않으면 업데이트 관리에서 해당 차원을 무시합니다.

    ![신호 논리 구성](./media/manage-updates-for-vm/signal-logic.png)

1. **경고 논리** 에서 **시간 집계** 및 **임계값** 필드에 값을 입력 한 다음 **완료** 를 선택 합니다.

1. 다음 페이지에서 경고에 대 한 이름 및 설명을 입력 합니다.

1. **심각도** 필드는 성공적인 실행의 경우 **정보(심각도 2)** 로 설정하고, 실패한 실행의 경우 **정보(심각도 1)** 로 설정합니다.

    ![스크린샷 경고 규칙 이름, 설명 및 심각도 필드가 강조 표시 된 경고 세부 정보 정의 섹션을 보여 줍니다.](./media/manage-updates-for-vm/define-alert-details.png)

1. **예** 를 선택 하 여 경고 규칙을 사용 하도록 설정 합니다.

## <a name="configure-action-groups-for-your-alerts"></a>경고에 대한 작업 그룹 구성

경고가 구성되었으면 여러 경고에서 사용할 작업 그룹을 설정할 수 있습니다. 작업에는 전자 메일 알림, runbook, 웹 후크 등이 포함 될 수 있습니다. 작업 그룹에 대해 자세히 알아보려면 [작업 그룹 만들기 및 관리](../../azure-monitor/alerts/action-groups.md)를 참조하세요.

1. 경고를 선택한 다음 **작업** 아래에서 **작업 그룹 추가** 를 선택 합니다. 그러면 **이 경고 규칙에 연결할 작업 그룹을 선택** 하십시오. 창이 표시 됩니다.

   :::image type="content" source="./media/manage-updates-for-vm/select-an-action-group.png" alt-text="사용량 및 예상 비용":::

1. 연결할 작업 그룹의 확인란을 선택 하 고 선택을 누릅니다.

## <a name="next-steps"></a>다음 단계

* [Azure Monitor의 경고](../../azure-monitor/alerts/alerts-overview.md)에 대해 자세히 알아보세요.

* Log Analytics 작업 영역에서 데이터를 검색 하 고 분석 하는 [로그 쿼리에](../../azure-monitor/logs/log-query-overview.md) 대해 알아봅니다.

* [Azure Monitor 로그를 사용 하 여 사용량 및 비용](../../azure-monitor/logs/manage-cost-storage.md) 관리 데이터 보존 기간을 변경 하 여 비용을 제어 하는 방법 및 데이터 사용량을 분석 하 고 경고 하는 방법을 설명 합니다.