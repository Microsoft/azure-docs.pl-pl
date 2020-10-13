---
title: plik dołączania
description: plik dołączania
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 03/05/2020
ms.openlocfilehash: ff449626ce528cfe0218a95330a567303c547e5f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "79486147"
---
1. Aby zainstalować zestaw SDK Azure Machine Learning dla języka Python, Skorzystaj z instrukcji w [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)  .

1. Utwórz [obszar roboczy Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).

1. Napisz plik  [konfiguracyjny](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.jswłączony**).

1. Sklonuj [repozytorium GitHub](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Uruchom serwer notesów z poziomu sklonowanego katalogu.

    ```bash
    jupyter notebook
    ```