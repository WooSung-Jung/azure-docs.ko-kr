---
title: Azure IoT Hub 준수를 위한 장치 업데이트 이해 | Microsoft Docs
description: 장치 업데이트 준수를 측정 하 Azure IoT Hub 장치 업데이트를 이해 합니다.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ac6094efde8a32b1fcc04c55bbc537afeb4166f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101663418"
---
# <a name="device-update-compliance"></a>장치 업데이트 준수

IoT Hub에 대 한 장치 업데이트에서 호환성은 가장 높은 버전 호환 업데이트가 설치 된 장치 수를 측정 합니다. 호환 되는 가장 높은 버전의 사용 가능한 업데이트를 설치 하면 장치가 규정을 준수 합니다. 

예를 들어 다음 업데이트를 사용 하 여 장치 업데이트 인스턴스를 살펴보겠습니다.

|업데이트 이름|업데이트 버전|호환 되는 장치 모델|
|-----------|--------------|-----------------------|
|업데이트 1    |1.0    |Model1|
|업데이트 2    |1.0    |Model2|
|업데이트 3    |2.0    |Model1|

다음 배포가 생성 되었다고 가정해 보겠습니다.

|배포 이름    |업데이트 이름    |대상 그룹|
|-----------|--------------|-------------------|
|Deployment1    |업데이트 1    |Group1|
|Deployment2    |업데이트 2    |Group2|
|Deployment3    |업데이트 3    |그룹 3|

이제 그룹 멤버 자격 및 설치 된 버전과 함께 다음 장치를 고려 합니다.

|DeviceId   |장치 모델   |설치 된 업데이트 버전|그룹 |규정 준수|
|-----------|--------------|-----------------------|-----|---------|
|Device1    |Model1 |1.0    |Group1 |새 업데이트 사용 가능</span>|
|Device2    |Model1 |2.0    |그룹 3 |최신 업데이트|
|Device3    |Model2 |1.0    |Group2 |최신 업데이트|
|장치 4    |Model1 |1.0    |그룹 3 |업데이트 진행 중|

장치 1 및 장치 4는 장치 업데이트 인스턴스에서 해당 모델과 호환 되는 더 높은 버전의 업데이트 3 업데이트 인 경우에도 버전 1.0가 설치 되어 있으므로 규격이 아닙니다. 장치 2 및 장치 3는 해당 모델에 대해 호환 되는 버전 업데이트를 설치 하기 때문에 모두 호환 됩니다.

준수는 업데이트를 장치의 그룹에 배포할지 여부를 고려 하지 않습니다. 장치 업데이트에 게시 된 모든 업데이트를 확인 합니다. 따라서 위 예제에서 장치 1가 배포 된 업데이트를 설치한 경우에도 비준수로 간주 됩니다. 장치 1는 업데이트 3를 성공적으로 설치 하기 전까지 비준수로 간주 됩니다. 준수 상태는 새 배포가 필요한 지 여부를 식별 하는 데 도움이 될 수 있습니다. 

위에서 설명한 것 처럼 IoT Hub 장치 업데이트에는 다음과 같은 세 가지 준수 상태가 있습니다.

*   **최신 업데이트** -장치에서 장치 업데이트에 게시 된 가장 높은 버전의 호환 업데이트를 설치 했습니다.
*   **업데이트 진행** 중 – 활성 배포는 장치에 가장 높은 버전의 호환 업데이트를 제공 하는 중입니다.
*   **새 업데이트 사용 가능** -장치가 아직 가장 높은 버전의 호환성 업데이트를 설치 하지 않았으며 해당 업데이트에 대 한 활성 배포에 있지 않습니다.
