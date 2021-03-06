---
title: 값 잘라내기
titleSuffix: Azure Machine Learning
description: Azure Machine Learning의 클립 값 모듈을 사용 하 여 이상 값을 검색 하 고 해당 값을 자르는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 99fb41542dff28997438881abad71da11e927a78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96014893"
---
# <a name="clip-values"></a>값 잘라내기

이 문서에서는 Azure Machine Learning 디자이너의 모듈을 설명 합니다.

자르기 값 모듈을 사용 하 여 지정 된 임계값 보다 높거나 낮은 데이터 값을 식별 하 고 필요에 따라 평균, 상수 또는 다른 대체 값으로 바꿀 수 있습니다.  

모듈을 클리핑할 숫자가 포함 된 데이터 집합에 연결 하 고 사용할 열을 선택한 다음 임계값 또는 값 범위와 대체 메서드를 설정 합니다. 모듈은 결과 또는 원래 데이터 집합에 추가 된 변경 된 값만 출력할 수 있습니다.

## <a name="how-to-configure-clip-values"></a>클립 값을 구성 하는 방법

시작 하기 전에 클리핑 하려는 열 및 사용할 메서드를 확인 합니다. 먼저 작은 데이터 하위 집합에 대 한 클리핑 메서드를 테스트 하는 것이 좋습니다.

이 모듈은 선택 영역에 포함 된 **모든** 열에 동일한 조건 및 대체 방법을 적용 합니다. 따라서 변경 하지 않을 열은 제외 해야 합니다.

일부 열에 클리핑 메서드나 다른 조건을 적용 해야 하는 경우 각 유사한 열 집합에 대해 **클립 값** 의 새 인스턴스를 사용 해야 합니다.

1.  파이프라인에 **클립 값** 모듈을 추가 하 고 수정 하려는 데이터 집합에 연결 합니다. **크기 조정 및 축소** 범주의 **데이터 변환** 에서이 모듈을 찾을 수 있습니다. 
  
1.  열 **목록** 에서 열 선택기를 사용 하 여 **클립 값** 이 적용 될 열을 선택 합니다.  
  
1.  **임계값 집합** 의 경우 드롭다운 목록에서 다음 옵션 중 하나를 선택 합니다. 이러한 옵션은 허용 되는 값과 잘리지 않아야 하는 값의 상한 및 하 한을 설정 하는 방법을 결정 합니다.  
  
    - **ClipPeaks**: 값을 최대 단위로 자르는 경우에는 상한을 지정 합니다. 해당 경계 값 보다 큰 값이 대체 됩니다.
  
    -  **ClipSubpeaks**: 하위 최대값으로 값을 자르는 경우에는 하 한만 지정 합니다. 해당 경계 값 보다 작은 값이 대체 됩니다.  
  
    - **ClipPeaksAndSubpeaks**: 값을 최대 및 하위 최대 수로 자르는 경우 상한 및 하 한 경계를 모두 지정할 수 있습니다. 이 범위를 벗어나는 값이 대체 됩니다. 경계 값과 일치 하는 값은 변경 되지 않습니다.
  
1.  이전 단계에서 선택한 내용에 따라 다음 임계값을 설정할 수 있습니다. 

    + 하 한 **임계값**: **ClipSubPeaks** 을 선택한 경우에만 표시 됩니다.
    + **상한 임계값**: **ClipPeaks** 을 선택한 경우에만 표시 됩니다.
    + **임계값**: **ClipPeaksAndSubPeaks** 을 선택한 경우에만 표시 됩니다.

    각 임계값 유형에 대해 **상수** 또는 **백분위** 수를 선택 합니다.

1. **상수** 를 선택 하는 경우 입력란에 최 댓 값 또는 최 댓 값을 입력 합니다. 예를 들어 999 값이 자리 표시자 값으로 사용 되었음을 알고 있다고 가정 합니다. 상한 임계값에 대해 **상수** 를 선택 하 고 **상한 임계값에 대해 상수 값** 에 999을 입력할 수 있습니다.
  
1. **백분위** 수를 선택 하는 경우 열 값을 백분위 수 범위로 제한 합니다. 

    예를 들어 10-80 백분위 수 범위의 값만 유지 하 고 다른 모든 항목을 대체 하려는 경우를 가정해 보겠습니다. **백분위** 수를 선택한 다음 **낮은 임계값에** 대 한 백분위 수 값으로 10을 입력 하 고, **상한 임계값으로 백분위 수 값** 에 80을 입력 합니다. 

    백분위 수 범위를 사용 하는 방법에 대 한 몇 가지 예는 [백분위 수](#examples-for-clipping-using-percentiles) 의 섹션을 참조 하세요.  
  
1.  대체 값을 정의 합니다.

    지정 된 경계와 정확히 일치 하는 숫자는 허용 되는 값 범위 내에 있는 것으로 간주 되므로 대체 되지 않습니다. 지정 된 범위를 벗어나는 모든 숫자가 대체 값으로 바뀝니다. 
  
    + **최대 값 대체 값**: 지정 된 임계값 보다 큰 모든 열 값을 대체할 값을 정의 합니다.  
    + **Subpeaks의 대체 값**: 지정 된 임계값 보다 작은 모든 열 값에 대 한 대체 값으로 사용할 값을 정의 합니다.  
    + **ClipPeaksAndSubpeaks** 옵션을 사용 하는 경우 위쪽 및 아래쪽 잘린 값에 대해 별도의 대체 값을 지정할 수 있습니다.  

    지원 되는 대체 값은 다음과 같습니다.  
  
    -   **Threshold**: 잘린 값을 지정 된 임계값으로 바꿉니다.  
  
    -   **Mean**: 잘린 값을 열 값의 평균으로 바꿉니다. 평균은 값이 잘리지 않기 전에 계산 됩니다.  
  
    -   **Median**: 잘린 값을 열 값의 중앙값으로 바꿉니다. 중앙값은 값이 잘리지 않기 전에 계산 됩니다.   
  
    -   **누락** 되었습니다. 잘린 값을 누락 된 (비어 있는) 값으로 바꿉니다.  
  
1.  **표시기 열 추가**: 지정 된 클리핑 작업을 해당 행의 데이터에 적용할지 여부를 알려 주는 새 열을 생성 하려면이 옵션을 선택 합니다. 이 옵션은 새 클리핑 및 대체 값 집합을 테스트 하는 경우에 유용 합니다.  
  
1. **덮어쓰기 플래그**: 새 값을 생성 하는 방법을 지정 합니다. 기본적으로 **자르기 값** 은 최고 값이 원하는 임계값으로 잘린 새 열을 생성 합니다. 새 값은 원래 열을 덮어씁니다.  
  
    원래 열을 유지 하 고 잘린 값이 있는 새 열을 추가 하려면이 옵션의 선택을 취소 합니다.  
  
1.  파이프라인을 제출합니다.  
  
    **클립 값** 모듈을 마우스 오른쪽 단추로 클릭 하 고 **시각화** **를 선택** 하거나 모듈을 선택 하 고 오른쪽 패널의 **출력** 탭으로 전환 하 여 값을 검토 하 고 클리핑 작업이 예상과 일치 하는지 확인 합니다.  
 
### <a name="examples-for-clipping-using-percentiles"></a>백분위 수를 사용 하 여 클리핑 예제

백분위수를 사용한 자르기의 작동 방식을 이해하기 위해 행이 10개인 데이터 집합의 각 행에 값 1-10이 하나씩 포함되어 있다고 가정해 보겠습니다.  
  
- 상한 임계값으로 백분위수를 사용하는 경우 90번째 백분위수의 값에서 데이터 집합 내 모든 값의 90%는 해당 값보다 작아야 합니다.  
  
- 하한 임계값으로 백분위수를 사용하는 경우 10번째 백분위수의 값에서 데이터 집합 내 모든 값의 10%는 해당 값보다 작아야 합니다.  
  
1.  **임계값 집합** 에 대해 **ClipPeaksAndSubPeaks** 를 선택 합니다.  
  
1.  **상한 임계값** 에 대해 **백분위** 수를 선택 하 고 **백분위 수** 에 대해 90을 입력 합니다.  
  
1.  **대문자 대체 값** 으로는 **누락 값** 을 선택 합니다.  
  
1.  **하한 임계값** 에는 **백분위수** 를 선택하고 **백분위수 값** 으로는 10을 입력합니다.  
  
1.  **하위 대체 값** 으로는 **누락 값** 을 선택 합니다.  
  
1.  **플래그 덮어쓰기** 옵션을 선택 취소 하 고 **표시기 열 추가** 옵션을 선택 합니다.  
  
이제 60을 하위 백분위 수 임계값으로 사용 하 여 동일한 파이프라인을 시도 하 고 임계값을 대체 값으로 사용 합니다. 다음 표에는 두 결과를 비교한 내용이 나와 있습니다.  
  
1.  Missing으로 바꾸기 상한 임계값 = 90; 하 한 임계값 = 20  
  
1.  Threshold로 바꾸기 상한 백분위 수 = 60; 하위 백분위 수 = 40  
  
|원래 데이터|누락 값으로 바꾸기|임계값으로 바꾸기|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|TRUE<br /><br /> TRUE<br /><br /> 3, FALSE<br /><br /> 4, FALSE<br /><br /> 5, FALSE<br /><br /> 6, FALSE<br /><br /> 7, FALSE<br /><br /> 8, FALSE<br /><br /> 9, FALSE<br /><br /> TRUE|4, TRUE<br /><br /> 4, TRUE<br /><br /> 4, TRUE<br /><br /> 4, TRUE<br /><br /> 5, FALSE<br /><br /> 6, FALSE<br /><br /> 7, TRUE<br /><br /> 7, TRUE<br /><br /> 7, TRUE<br /><br /> 7, TRUE| 
 
## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 
