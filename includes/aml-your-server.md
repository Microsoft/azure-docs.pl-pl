---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 03/05/2020
ms.openlocfilehash: 0d7622217d192a100fd809c32c9e85970cf61fd7
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510966"
---
1. Aby zainstalować zestaw SDK Azure Machine Learning dla języka Python, Skorzystaj z instrukcji w [Azure Machine Learning SDK](/python/api/overview/azure/ml/install)  .

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