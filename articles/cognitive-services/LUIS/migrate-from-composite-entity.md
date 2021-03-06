---
title: 복합 엔터티 업그레이드-LUIS
description: LUIS 포털에서 업그레이드 프로세스를 사용 하 여 복합 엔터티를 기계 학습 엔터티로 업그레이드 합니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 46e9ece70d9f980065c719ee1205eb46591b45c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025245"
---
# <a name="upgrade-composite-entity-to-machine-learning-entity"></a>복합 엔터티를 기계 학습 엔터티로 업그레이드

복합 엔터티를 기계 학습 엔터티로 업그레이드 하 여 엔터티를 디버깅 하는 데 더 나은 decomposability으로 더 완벽 한 예측을 받는 엔터티를 빌드합니다.

## <a name="current-version-model-restrictions"></a>현재 버전 모델 제한

업그레이드 프로세스는 앱에 있는 기존 복합 엔터티를 기반으로 하는 기계 학습 엔터티를 새 버전의 앱에 만듭니다. 여기에는 복합 엔터티 자식 및 역할이 포함 됩니다. 또한이 프로세스는 예제 길이 발언의 레이블을 새 엔터티를 사용 하도록 전환 합니다.

## <a name="upgrade-process"></a>업그레이드 프로세스

업그레이드 프로세스는 다음과 같습니다.
* 각 복합 엔터티에 대해 새 machine learning 엔터티를 만듭니다.
* 자식 엔터티:
    * 자식 엔터티가 복합 엔터티에서 사용 되는 경우 기계 학습 엔터티에만 추가 됩니다.
    * 자식 엔터티를 복합에서 사용 하 _고_ 별도의 엔터티 (예: 길이 발언)로 사용 하는 경우이 엔터티는 버전의 엔터티에 추가 되 고 새 machine learning 엔터티에 하위 엔터티로 추가 됩니다.
    * 자식 엔터티에서 역할을 사용 하는 경우 각 역할은 동일한 이름의 하위 엔터티로 변환 됩니다.
    * 자식 엔터티가 비 기계어 학습 엔터티 (정규식, 목록 엔터티 또는 미리 작성 된 엔터티) 인 경우에는 동일한 이름을 사용 하 여 새 하위 엔터티가 만들어지고 새 하위 엔터티에는 필요한 기능이 추가 된 비 기계어 학습 엔터티를 사용 하는 기능이 있습니다.
* 이름은 유지 되지만 동일한 하위 엔터티/형제 수준에서 고유 해야 합니다. [고유 이름 제한을](./luis-limits.md#name-uniqueness)참조 하세요.
* 예 길이 발언의 레이블은 subentities를 사용 하 여 새 기계 학습 엔터티로 전환 됩니다.

다음 차트를 사용 하 여 모델이 어떻게 변경 되는지 이해할 수 있습니다.

|이전 개체|새 개체|참고|
|--|--|--|
|복합 엔터티|구조를 사용 하는 기계 학습 엔터티|두 개체는 모두 부모 개체입니다.|
|복합의 자식 엔터티는 **단순 엔터티입니다** .|대상|두 개체는 모두 자식 개체입니다.|
|복합의 자식 엔터티는 Number와 같은 미리 작성 된 **엔터티입니다** .|number 및 대상와 같이 미리 작성 된 엔터티의 이름이 인 하위 엔터티에는 제약 조건이 _true_ 로 설정 된 미리 작성 된 number 엔터티의 _기능이_ 있습니다.|대상에 하위 엔터티 수준에서 제약 조건이 있는 기능이 포함 되어 있습니다.|
|복합의 자식 엔터티는 **미리** 작성 된 엔터티 (예: Number) 이며 미리 작성 된 엔터티에는 **역할이** 있습니다.|이름이 role 인 하위 엔터티 및 대상에는 미리 작성 된 Number 엔터티의 기능이 제약 조건 옵션이 true로 설정 되어 있습니다.|대상에 하위 엔터티 수준에서 제약 조건이 있는 기능이 포함 되어 있습니다.|
|역할|대상|역할 이름은 하위 엔터티 이름이 됩니다. 대상는 machine learning 엔터티의 직계 하위 항목입니다.|

## <a name="begin-upgrade-process"></a>업그레이드 프로세스 시작

업데이트 하기 전에 다음을 확인 해야 합니다.

* 업그레이드할 올바른 버전이 아닌 경우 버전을 변경 합니다.


1. 알림에서 업그레이드 프로세스를 시작 하거나 예약 된 다음 메시지가 나타날 때까지 기다릴 수 있습니다.

    > [!div class="mx-imgBorder"]
    > ![알림에서 업그레이드 시작](./media/update-composite-entity/notification-begin-update.png)

1. 팝업에서 **지금 업그레이드** 를 선택 합니다.

1. 정보를 **업그레이드할 때 발생** 하는 상황을 검토 하 고 **계속** 을 선택 합니다.

1. 목록에서 업그레이드할 복합 엔터티를 선택 하 고 **계속** 을 선택 합니다.

1. 확인란을 선택 하 여 학습 되지 않은 모든 변경 내용을 현재 버전에서 업그레이드 된 버전으로 이동할 수 있습니다.

1. **계속** 을 선택 하 여 업그레이드 프로세스를 시작 합니다.

1. 진행률 표시줄은 업그레이드 프로세스의 상태를 나타냅니다.

1. 프로세스가 완료 되 면 새 machine learning 엔터티를 사용 하 여 새로운 학습 된 버전을 사용할 수 있습니다. 새 엔터티를 **사용해 보세요** .를 선택 하 여 새 엔터티를 확인 합니다.

    새 엔터티에 대 한 업그레이드 또는 교육이 실패 한 경우 알림이 추가 정보를 제공 합니다.

1. 엔터티 목록 페이지에서 새 엔터티는 형식 이름 옆에 **새로 만들기** 로 표시 됩니다.

## <a name="next-steps"></a>다음 단계

* [작성자 및 협력자](luis-how-to-collaborate.md)