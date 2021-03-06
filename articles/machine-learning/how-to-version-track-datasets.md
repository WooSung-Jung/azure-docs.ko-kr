---
title: 데이터 집합 버전 관리
titleSuffix: Azure Machine Learning
description: 기계 학습 데이터 집합의 버전을 지정 하는 방법 및 기계 학습 파이프라인에서 버전 관리가 작동 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: fde25e4ba75bfb86c9837582d7168f85335836b6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520543"
---
# <a name="version-and-track-azure-machine-learning-datasets"></a>버전 및 추적 Azure Machine Learning 데이터 집합

이 문서에서는 재현 가능성에 대 한 Azure Machine Learning 데이터 집합을 버전 및 추적 하는 방법을 알아봅니다. 데이터 집합 버전 관리는 이후 실험을 위해 데이터 집합의 특정 버전을 적용할 수 있도록 데이터의 상태에 책갈피를 지정 하는 방법입니다.

일반적인 버전 관리 시나리오는 다음과 같습니다.

* 새 데이터를 다시 학습에 사용할 수 있는 경우
* 다른 데이터 준비 또는 기능 엔지니어링 방법을 적용 하는 경우

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에서는 다음이 필요합니다.

- [Python 용 AZURE MACHINE LEARNING SDK가 설치 되어](/python/api/overview/azure/ml/install)있습니다. 이 SDK는 [azureml 데이터 집합](/python/api/azureml-core/azureml.core.dataset) 패키지를 포함 합니다.
    
- [Azure Machine Learning 작업 영역](concept-workspace.md)입니다. 다음 코드를 실행 하 여 기존 항목을 검색 하거나 [새 작업 영역을 만듭니다](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Azure Machine Learning 데이터 집합](how-to-create-register-datasets.md)입니다.

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>데이터 집합 버전 등록 및 검색

데이터 집합을 등록 하면 실험 및 동료 간에 버전을 다시 사용 하 고 공유할 수 있습니다. 동일한 이름으로 여러 데이터 집합을 등록 하 고 이름 및 버전 번호로 특정 버전을 검색할 수 있습니다.

### <a name="register-a-dataset-version"></a>데이터 집합 버전 등록

다음 코드는 `titanic_ds` 매개 변수를로 설정 하 여 새 버전의 데이터 집합을 등록 합니다 `create_new_version` `True` . `titanic_ds`작업 영역에 등록 된 기존 데이터 집합이 없는 경우 코드는 이름을 사용 하 여 새 데이터 집합을 만들고 `titanic_ds` 해당 버전을 1로 설정 합니다.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>이름을 기준으로 데이터 집합 검색

기본적으로 클래스의 [get_by_name ()](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) 메서드는 `Dataset` 작업 영역에 등록 된 데이터 집합의 최신 버전을 반환 합니다. 

다음 코드는 데이터 집합의 버전 1을 가져옵니다 `titanic_ds` .

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>버전 관리 모범 사례

데이터 집합 버전을 만들 때 작업 영역을 사용 하 여 데이터의 추가 복사본을 만들지 *않습니다* . 데이터 집합은 저장소 서비스의 데이터에 대 한 참조 이므로 저장소 서비스에서 관리 하는 단일 원인이 있습니다.

>[!IMPORTANT]
> 데이터 집합에서 참조 하는 데이터를 덮어쓰거나 삭제 하면 특정 버전의 데이터 집합을 호출 해도 변경 내용이 되돌리지 *않습니다* .

데이터 집합에서 데이터를 로드 하는 경우 데이터 집합에서 참조 하는 현재 데이터 콘텐츠가 항상 로드 됩니다. 각 데이터 집합 버전을 재현할 수 있는지 확인 하려면 데이터 집합 버전에서 참조 하는 데이터 콘텐츠를 수정 하지 않는 것이 좋습니다. 새 데이터가 들어오면 새 데이터 파일을 별도의 데이터 폴더에 저장 한 다음 새 데이터 집합 버전을 만들어 새 폴더의 데이터를 포함 합니다.

다음 이미지 및 샘플 코드는 데이터 폴더를 구조화 하 고 해당 폴더를 참조 하는 데이터 집합 버전을 만드는 데 권장 되는 방법을 보여 줍니다.

![폴더 구조](./media/how-to-version-track-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-an-ml-pipeline-output-dataset"></a>ML 파이프라인 출력 데이터 집합 버전

각 [ML 파이프라인](concept-ml-pipelines.md) 단계의 입력 및 출력으로 데이터 집합을 사용할 수 있습니다. 파이프라인을 다시 실행 하면 각 파이프라인 단계의 출력이 새 데이터 집합 버전으로 등록 됩니다.

ML 파이프라인은 파이프라인이 다시 실행 될 때마다 각 단계의 출력을 새 폴더로 채웁니다. 이 동작으로 버전이 지정 된 출력 데이터 집합을 재현할 수 있습니다. [파이프라인의 데이터 집합](./how-to-create-machine-learning-pipelines.md#steps)에 대해 자세히 알아보세요.

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-data-in-your-experiments"></a>실험에서 데이터 추적

Azure Machine Learning는 전체 실험에서 데이터를 입력 및 출력 데이터 집합으로 추적 합니다.  

데이터를 **입력 데이터 집합** 으로 추적 하는 시나리오는 다음과 같습니다. 

* `DatasetConsumptionConfig` `inputs` `arguments` `ScriptRunConfig` 실험 실행을 제출할 때 개체의 또는 매개 변수를 통해 개체로 사용할 수도 있습니다. 

* 과 같은 메서드가 get_by_name () 또는 get_by_id ()가 스크립트에서 호출 됩니다. 이 시나리오의 경우 작업 영역에 등록할 때 데이터 집합에 할당 되는 이름이 표시 됩니다. 

데이터를 **출력 데이터 집합** 으로 추적 하는 시나리오는 다음과 같습니다.  

* `OutputFileDatasetConfig` `outputs` `arguments` 실험 실행을 제출할 때 또는 매개 변수를 통해 개체를 전달 합니다. `OutputFileDatasetConfig` 개체를 사용 하 여 파이프라인 단계 간에 데이터를 유지할 수도 있습니다. [ML 파이프라인 간 데이터 이동 단계를](how-to-move-data-in-out-of-pipelines.md) 참조 하세요.
  
* 스크립트에 데이터 집합을 등록 합니다. 이 시나리오의 경우 작업 영역에 등록할 때 데이터 집합에 할당 되는 이름이 표시 됩니다. 다음 예제에서 `training_ds` 는 표시 되는 이름입니다.

    ```Python
   training_ds = unregistered_ds.register(workspace = workspace,
                                     name = 'training_ds',
                                     description = 'training data'
                                     )
    ```

* 스크립트에서 등록 되지 않은 데이터 집합을 사용 하 여 자식 실행을 제출 합니다. 그러면 익명으로 저장 된 데이터 집합이 생성 됩니다.

### <a name="trace-datasets-in-experiment-runs"></a>실험 실행의 데이터 집합 추적

각 Machine Learning 실험에 대해 실험 개체에서 입력으로 사용 되는 데이터 집합을 쉽게 추적할 수 있습니다 `Run` .

다음 코드에서는 메서드를 사용 하 여 [`get_details()`](/python/api/azureml-core/azureml.core.run.run#get-details--) 실험 실행에 사용 된 입력 데이터 집합을 추적 합니다.

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

`input_datasets` [Azure Machine Learning studio]()를 사용 하 여 실험에서을 찾을 수도 있습니다. 

다음 이미지는 Azure Machine Learning studio에서 실험의 입력 데이터 집합을 찾을 수 있는 위치를 보여 줍니다. 이 예에서는 **실험** 창으로 이동 하 여 실험의 특정 실행에 대 한 **속성** 탭을 엽니다 `keras-mnist` .

![입력 데이터 집합](./media/how-to-version-track-datasets/input-datasets.png)

데이터 집합을 사용 하 여 모델을 등록 하려면 다음 코드를 사용 합니다.

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

등록 후에는 Python을 사용 하 여 데이터 집합에 등록 된 모델 목록을 확인 하거나 [스튜디오](https://ml.azure.com/)로 이동할 수 있습니다.

다음 뷰는 **자산** 아래의 **데이터 집합** 창에서 가져온 것입니다. 데이터 집합을 선택 하 고 **모델** 탭을 선택 하 여 데이터 집합에 등록 된 모델 목록을 표시 합니다. 

![입력 데이터 집합 모델](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>다음 단계

* [데이터 세트로 학습](how-to-train-with-datasets.md)
* [추가 샘플 데이터 집합 전자 필기장](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)