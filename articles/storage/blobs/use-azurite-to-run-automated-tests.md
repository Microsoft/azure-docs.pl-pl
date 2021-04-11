---
title: Uruchamianie testów automatycznych przy użyciu azurite
titleSuffix: Azure Storage
description: Dowiedz się, jak pisać testy automatyczne dla prywatnych punktów końcowych usługi Azure Blob Storage przy użyciu azurite.
services: storage
author: ikivanc
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 03/31/2021
ms.author: ikivanc
ms.openlocfilehash: c4e8a11e0c46cb9a138a1a66060d9fdcc72c192e
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111819"
---
# <a name="run-automated-tests-by-using-azurite"></a>Uruchamianie testów automatycznych przy użyciu azurite

Dowiedz się, jak pisać testy automatyczne dla prywatnych punktów końcowych usługi Azure Blob Storage przy użyciu emulatora magazynu azurite.

## <a name="run-tests-on-your-local-machine"></a>Uruchom testy na komputerze lokalnym

1. Zainstaluj najnowszą wersję środowiska [Python](https://www.python.org/)

1. Zainstaluj [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/)

1. Zainstaluj i uruchom program [azurite](../common/storage-use-azurite.md):

   **Opcja 1:** Użyj npm, aby zainstalować, a następnie Uruchom lokalnie azurite

   ```bash
   # Install Azurite
   npm install -g azurite
   
   # Create an Azurite directory
   mkdir c:/azurite
   
   # Launch Azurite locally
   azurite --silent --location c:\azurite --debug c:\azurite\debug.log
   ```

   **Opcja 2:** Używanie platformy Docker do uruchamiania azurite

   ```bash
   docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite azurite-blob --blobHost 0.0.0.0
   ```

1. W obszarze Eksplorator usługi Azure Storage wybierz pozycję **Dołącz do lokalnego emulatora**

    :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection.png" alt-text="Zrzut ekranu przedstawiający Eksplorator usługi Azure Storage łączenia się ze źródłem usługi Azure Storage.":::

1. Podaj **nazwę wyświetlaną** i numer **portu obiektów BLOB** , aby połączyć azurite i użyć Eksplorator usługi Azure Storage do zarządzania lokalnym magazynem obiektów BLOB.

   :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection-attach.png" alt-text="Zrzut ekranu przedstawiający Eksplorator usługi Azure Storage dołączania do lokalnego emulatora.":::

1. Tworzenie wirtualnego środowiska języka Python

   ```bash
   python -m venv .venv
   ```

1. Utwórz kontener i zainicjuj zmienne środowiskowe. Użyj pliku [](https://docs.pytest.org/) [conftest.py](https://docs.pytest.org/en/2.1.0/plugins.html) PyTest, aby wygenerować testy. Oto przykład pliku conftest.py:

   ```python
   from azure.storage.blob import BlobServiceClient
   import os

   def pytest_generate_tests(metafunc):
      os.environ['AZURE_STORAGE_CONNECTION_STRING'] = 'DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;'
      os.environ['STORAGE_CONTAINER'] = 'test-container'

      # Create a container for Azurite for the first run
      blob_service_client = BlobServiceClient.from_connection_string(os.environ.get("AZURE_STORAGE_CONNECTION_STRING"))
      try:
         blob_service_client.create_container(os.environ.get("STORAGE_CONTAINER"))
      except Exception as e:
         print(e)
   ```

   > [!NOTE]
   > Wartość pokazana dla `AZURE_STORAGE_CONNECTION_STRING` jest wartością domyślną dla azurite, ale nie jest kluczem prywatnym.

1. Zainstaluj zależności wymienione w pliku [requirements.txt](https://github.com/Azure-Samples/automated-testing-with-azurite/blob/main/requirements.txt)

   ```bash
   pip install -r requirements.txt
   ```

1. Uruchom testy:

   ```bash
   python -m pytest ./tests
   ```

Po uruchomieniu testów można wyświetlić pliki w azurite BLOB Storage za pomocą Eksplorator usługi Azure Storage.

:::image type="content" source="media/use-azurite-to-run-automated-tests/http-local-blob-storage.png" alt-text="Zrzut ekranu przedstawiający Eksplorator usługi Azure Storage wyświetlenia plików generowanych przez testy.":::

## <a name="run-tests-on-azure-pipelines"></a>Uruchom testy na Azure Pipelines

Po uruchomieniu testów lokalnie upewnij się, że testy zachodzą na [Azure Pipelines](/azure/devops/pipelines). Użyj obrazu Docker azurite jako agenta hostowanego na platformie Azure lub użyj npm do zainstalowania azurite. Poniższy Azure Pipelines przykład używa npm do instalacji azurite.
  
```yaml
trigger:
- master

steps:
- task: UsePythonVersion@0
  displayName: 'Use Python 3.7'
  inputs:
    versionSpec: 3.7

- bash: |
    pip install -r requirements_tests.txt
  displayName: 'Setup requirements for tests'
  
- bash: |
    sudo npm install -g azurite
    sudo mkdir azurite
    sudo azurite --silent --location azurite --debug azurite\debug.log &
  displayName: 'Install and Run Azurite'

- bash: |
    python -m pytest --junit-xml=unit_tests_report.xml --cov=tests --cov-report=html --cov-report=xml ./tests
  displayName: 'Run Tests'

- task: PublishCodeCoverageResults@1
  inputs:
    codeCoverageTool: Cobertura
    summaryFileLocation: '$(System.DefaultWorkingDirectory)/**/coverage.xml'
    reportDirectory: '$(System.DefaultWorkingDirectory)/**/htmlcov'

- task: PublishTestResults@2
  inputs:
    testResultsFormat: 'JUnit'
    testResultsFiles: '**/*_tests_report.xml'
    failTaskOnFailedTests: true
```

Po uruchomieniu testów Azure Pipelines powinny zostać wyświetlone dane wyjściowe podobne do tego:

:::image type="content" source="media/use-azurite-to-run-automated-tests/azure-pipeline.png" alt-text="Zrzut ekranu przedstawiający wyniki testu Azure Pipelines.":::

## <a name="next-steps"></a>Następne kroki

- [Korzystanie z emulatora azurite na potrzeby tworzenia lokalnych magazynów platformy Azure](../common/storage-use-azurite.md)
- [Przykład: używanie azurite do uruchamiania testów usługi BLOB Storage w potoku Azure DevOps](https://github.com/Azure-Samples/automated-testing-with-azurite)
