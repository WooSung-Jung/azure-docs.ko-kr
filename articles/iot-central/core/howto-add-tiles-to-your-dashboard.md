---
title: Azure IoT Central 대시보드로 구성 | Microsoft Docs
description: 작성기로 타일을 사용 하 여 기본 Azure IoT Central 응용 프로그램 대시보드를 구성 하는 방법을 알아봅니다.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 12/19/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 73c78f514f974b97eb178177f12dc4ceff7ede63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97797012"
---
# <a name="configure-the-application-dashboard"></a>애플리케이션 대시보드 구성

**대시보드** 는 IoT Central 응용 프로그램에 연결할 때 표시 되는 첫 번째 페이지입니다. 업계 중심의 [응용 프로그램 템플릿](./concepts-app-templates.md)중 하나에서 응용 프로그램을 만드는 경우 응용 프로그램에는 미리 정의 된 대시보드가 시작 됩니다. 사용자 지정 [응용 프로그램 템플릿에서](./concepts-app-templates.md)응용 프로그램을 만드는 경우 대시보드는 시작 하기 위한 몇 가지 팁을 표시 합니다.

> [!TIP]
> 사용자는 기본 애플리케이션 대시보드 외에도 [여러 대시보드를 만들](howto-create-personal-dashboards.md) 수 있습니다. 해당 대시보드는 사용자에게만 제공되거나 애플리케이션의 모든 사용자가 공유할 수 있습니다.  

## <a name="add-tiles"></a>타일 추가

다음 스크린샷은 **사용자 지정 응용 프로그램** 템플릿에서 만든 응용 프로그램의 대시보드를 보여 줍니다. 현재 대시보드를 사용자 지정 하려면 **편집** 을 선택 하 고 사용자 지정 개인 또는 공유 대시보드를 추가 하려면 **새로 만들기** 를 선택 합니다.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="사용자 지정 응용 프로그램 템플릿을 기반으로 하는 응용 프로그램 대시보드":::

**편집** 또는 **새로 만들기** 를 선택 하면 대시보드가 *편집* 모드에 있습니다. 대시보드 **편집** 패널의 도구를 사용 하 여 타일을 대시보드에 추가 하 고 대시보드 자체에서 타일을 사용자 지정 및 제거할 수 있습니다. 예를 들어 **원격 분석** 타일을 추가 하 여 하나 이상의 장치에서 보고 하는 현재 온도를 표시 하려면 다음을 수행 합니다.

1. **장치 그룹** 을 선택 하 고 장치 드롭다운에서 타일에 표시할 장치를 **선택 합니다.** 이제 장치에서 사용 가능한 원격 분석, 속성 및 명령이 표시 됩니다.

1. 필요한 경우 드롭다운을 사용 하 여 타일에 표시할 원격 분석 값을 선택 합니다. **+ 원격 분석**, **+ 속성** 또는 **+ 클라우드 속성** 을 선택 하 여 다른 타일에 더 많은 항목을 추가할 수 있습니다.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="대시보드에 온도 원격 분석 타일 추가":::

타일에 표시할 값을 모두 선택 했으면 **타일 추가를 클릭 합니다.** 이제 타일은 시각화를 변경 하 고, 크기를 조정 하 고, 이동 하 고, 구성할 수 있는 대시보드에 표시 됩니다.

대시보드에서 타일 추가 및 사용자 지정을 완료 한 후에는 **저장** 을 선택 하 여 변경 내용을 대시보드에 저장 합니다. 그러면 편집 모드에서 제외 됩니다.

## <a name="customize-tiles"></a>타일 사용자 지정

타일을 편집 하려면 편집 모드 여야 합니다.  사용할 수 있는 사용자 지정 옵션은 [타일 유형에](#tile-types)따라 달라 집니다.

* 타일의 눈금자 아이콘을 사용 하 여 시각화를 변경할 수 있습니다. 시각화에는 꺾은선형 차트, 가로 막대형 차트, 원형 차트, 마지막으로 알려진 값, Kpi (핵심 성과 지표), 열 지도 및 지도가 포함 됩니다.

* 사각형 아이콘을 사용 하 여 타일의 크기를 조정할 수 있습니다.

* 기어 아이콘을 사용 하 여 시각화를 구성할 수 있습니다. 예를 들어 꺾은선형 차트를 시각화 하는 경우 범례와 축을 표시 하도록 선택 하 고 그릴 시간 범위를 선택할 수 있습니다.


## <a name="tile-types"></a>타일 유형

다음 표에서는 대시보드에 추가할 수 있는 다양 한 유형의 타일에 대해 설명 합니다.

| Tile             | 설명 |
| ---------------- | ----------- |
| Markdown         | Markdown 타일은 Markdown를 사용 하 여 서식이 지정 된 제목 및 설명 텍스트를 표시 하는 클릭할 수 있는 타일입니다. URL은 응용 프로그램의 다른 페이지에 대 한 상대 링크 또는 외부 사이트에 대 한 절대 링크 일 수 있습니다.|
| 이미지            | 이미지 타일은 사용자 지정 이미지를 표시하며 클릭할 수 있습니다. URL은 응용 프로그램의 다른 페이지에 대 한 상대 링크 또는 외부 사이트에 대 한 절대 링크 일 수 있습니다.|
| label            | 레이블 타일은 대시보드에 사용자 지정 텍스트를 표시합니다. 텍스트 크기를 선택할 수 있습니다. 레이블 타일을 사용하여 설명, 연락처 세부 정보 또는 도움말과 같은 관련 정보를 대시보드에 추가합니다.|
| 개수            | 개수 타일 장치 그룹의 장치 수를 표시 합니다.|
| 지도              | 지도 타일 지도에서 하나 이상의 장치 위치를 표시 합니다. 또한 디바이스 위치 기록으로 최대 100개 지점을 표시할 수 있습니다. 예를 들어 지난 주에 장치가 있는 위치의 샘플링 된 경로를 표시할 수 있습니다.|
| KPI              |  KPI 타일은 일정 기간 동안 하나 이상의 장치에 대 한 집계 원격 분석 값을 표시 합니다. 예를 들어 지난 1 시간 동안 하나 이상의 장치에 대 한 최대 온도 및 압력을 표시 하는 데 사용할 수 있습니다.|
| 꺾은선형 차트       | 꺾은선형 차트 타일 특정 기간 동안 하나 이상의 장치에 대해 하나 이상의 집계 원격 분석 값을 플롯 합니다. 예를 들어 꺾은선형 차트를 표시 하 여 지난 1 시간 동안 하나 이상의 장치에 대 한 평균 온도와 압력을 그릴 수 있습니다.|
| 가로 막대형 차트        | 가로 막대형 차트 타일 특정 기간 동안 하나 이상의 장치에 대 한 하나 이상의 집계 원격 분석 값을 플롯 합니다. 예를 들어 가로 막대형 차트를 표시 하 여 지난 1 시간 동안 하나 이상의 장치에 대 한 평균 온도 및 압력을 표시할 수 있습니다.|
| 원형 차트        | 원형 차트 타일은 특정 기간 동안 하나 이상의 장치에 대해 하나 이상의 집계 원격 분석 값을 표시 합니다.|
| 열 지도         | 열 지도 타일은 색으로 표시 된 하나 이상의 장치에 대 한 정보를 표시 합니다.|
| 마지막으로 알려진 값 | 마지막으로 알려진 값 타일 하나 이상의 장치에 대 한 최신 원격 분석 값을 표시 합니다. 예를 들어이 타일을 사용 하 여 하나 이상의 장치에 대 한 가장 최근의 온도, 압력 및 습도 값을 표시할 수 있습니다. |
| 이벤트 기록    | 이벤트 기록 타일은 일정 기간에 걸쳐 디바이스의 이벤트를 표시합니다. 예를 들어 지난 1 시간 동안 하나 이상의 장치에 대 한 모든 밸브 열기 및 닫기 이벤트를 표시 하는 데 사용할 수 있습니다.|
| 속성         |  속성 타일 하나 이상의 장치에 대 한 속성 및 클라우드 속성의 현재 값을 표시 합니다. 예를 들어이 타일을 사용 하 여 장치에 대 한 제조업체 또는 펌웨어 버전과 같은 장치 속성을 표시할 수 있습니다. |

현재 여러 장치를 지 원하는 타일에 최대 10 개의 장치를 추가할 수 있습니다.

### <a name="customizing-visualizations"></a>시각화 사용자 지정

기본적으로 꺾은선형 차트는 일정 시간 동안 데이터를 표시 합니다. 선택한 시간 범위는 50 같은 크기의 버킷으로 분할 되 고 장치 데이터는 버킷 당 집계 되어 선택한 시간 범위에 대해 50 데이터 요소를 제공 합니다. 원시 데이터를 보려면 선택 항목을 변경 하 여 최근 100 값을 볼 수 있습니다. 시간 범위를 변경 하거나 원시 데이터 시각화를 선택 하려면 **차트 구성** 패널에서 범위 표시 드롭다운을 사용 합니다.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/display-range.png" alt-text="꺾은선형 차트의 표시 범위 변경":::

집계 값을 표시 하는 타일의 경우 **차트 구성** 패널에서 원격 분석 유형 옆의 기어 아이콘을 선택 하 여 집계를 선택 합니다. 평균, 합계, 최대값, 최소값, 개수를 선택할 수 있습니다.

꺾은선형 차트, 가로 막대형 차트 및 원형 차트의 경우 다양 한 원격 분석 값의 색을 사용자 지정할 수 있습니다. 사용자 지정 하려는 원격 분석 옆에 있는 색상표 아이콘을 선택 합니다.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="원격 분석 값의 색 변경":::

문자열 속성 또는 원격 분석 값을 표시 하는 타일의 경우 텍스트를 표시 하는 방법을 선택할 수 있습니다. 예를 들어 장치에서 문자열 속성에 URL을 저장 하는 경우 클릭 가능한 링크로 표시할 수 있습니다. URL이 이미지를 참조하는 경우 마지막으로 알려진 값 또는 속성 타일로 이미지를 렌더링할 수 있습니다. 문자열이 표시 되는 방식을 변경 하려면 타일 구성에서 원격 분석 유형 또는 속성 옆의 기어 아이콘을 선택 합니다.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="타일에 문자열이 표시 되는 방식 변경":::

숫자 **KPI**, **마지막으로 알려진 값** 및 **속성** 타일의 경우 조건부 서식을 사용 하 여 현재 값을 기준으로 타일의 색을 사용자 지정할 수 있습니다. 조건부 서식을 추가 하려면 타일에서 **구성** 을 선택한 다음 사용자 지정할 값 옆에 있는 **조건부 서식** 아이콘을 선택 합니다.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-1.png" alt-text="타일에 대 한 구성 옵션을 찾는 방법 및 조건부 서식 아이콘을 찾는 방법을 보여 주는 스크린샷":::

조건부 서식 지정 규칙을 추가 합니다.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-2.png" alt-text="평균 흐름에 대 한 조건부 서식 규칙을 보여 주는 스크린샷 20 개 미만의 규칙 (20 개 미만)은 녹색, 50은 노란색, 50은 빨강입니다.":::
   
다음 스크린샷은 조건부 서식 지정 규칙의 효과를 보여 줍니다.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-3.png" alt-text="평균 워터 마크 흐름 타일의 빨간색 배경색을 보여 주는 스크린샷 타일의 번호는 50.54입니다.":::

### <a name="tile-formatting"></a>"타일" 서식 지정
KPI, LKV 및 속성 타일에서 사용할 수 있는이 기능을 통해 사용자는 글꼴 크기를 조정 하거나, 10 진수 전체 자릿수를 선택 하거나, 숫자 값의 약어를 지정 하거나 (예: 1700을 1.7 K 형식으로), 타일에서 문자열 값을 래핑할 수 있습니다.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/tile-format.png" alt-text="타일 형식":::

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central 기본 애플리케이션 대시보드를 구성하는 방법을 알아보았으므로 [개인 대시보드를 만드는 방법을 알아보겠습니다](howto-create-personal-dashboards.md).
