---
title: Omówienie notesów usługi Azure Synapse Analytics
description: Ten artykuł zawiera omówienie możliwości dostępnych za pomocą notesów usługi Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 5efdfcea7b6d727038bad124368179b9395bfafd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97093281"
---
# <a name="azure-synapse-analytics-notebooks"></a>Notesy usługi Azure Synapse Analytics

Notes programu Synapse Studio to interfejs sieci Web służący do tworzenia plików, które zawierają kod na żywo, wizualizacje i tekst opisowy. Notesy są dobrym miejscem do weryfikowania pomysłów i używania szybkich eksperymentów w celu uzyskania szczegółowych informacji na podstawie danych. 

Za pomocą notesu usługi Azure Synapse Studio można:

* Wprowadzenie do pracy z konfiguracją zero.
* Zachowaj bezpieczeństwo danych dzięki wbudowanym funkcjom zabezpieczeń przedsiębiorstwa.
* Analizuj dane w formatach RAW (CSV, txt, JSON itp.), przetwarzanych formatach plików (Parquet, Delta Lake, ORC itp.) i plikach danych tabelarycznych SQL dla platformy Spark i SQL.
* Wydajniej dzięki ulepszonym funkcjom tworzenia i wbudowanej wizualizacji danych.

Ta sekcja zawiera artykuły dotyczące mieszania języków, tworzenia wizualizacji danych, notesów parametryzacja, kompilowania potoków itp. Zawiera również odwołania i samouczki dotyczące sposobu rozpoczęcia pracy z programowaniem notesu.

## <a name="create-manage-and-use-notebooks"></a>Tworzenie notesów i zarządzanie nimi
Notesami można zarządzać przy użyciu interfejsu użytkownika programu Synapse Studio. 

Aby dowiedzieć się więcej na temat tworzenia notesów i zarządzania nimi, zobacz następujące artykuły:
  - Zarządzanie notesami
    - [Tworzenie notesów](./spark/../apache-spark-development-using-notebooks.md#create-a-notebook)
    - [Tworzenie notesów](./spark/../apache-spark-development-using-notebooks.md#develop-notebooks)
    - [Przenoszenie danych do notesu](./spark/../apache-spark-development-using-notebooks.md#bring-data-to-a-notebook)
    - [Korzystanie z wielu języków za pomocą poleceń Magic i tabel tymczasowych](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)
    - [Korzystanie z poleceń Magic komórek](./spark/../apache-spark-development-using-notebooks.md#magic-commands)
  - Opracowywanie zawartości
    - [Konfigurowanie ustawień sesji platformy Spark](./spark/../apache-spark-development-using-notebooks.md#spark-session-config)
    - [Korzystanie z narzędzi Microsoft Spark Utilities](./spark/../microsoft-spark-utilities.md)
    - [Wizualizowanie danych przy użyciu notesów i bibliotek](./spark/../apache-spark-data-visualization.md)
    - [Integrowanie notesu z potokami](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)


## <a name="next-steps"></a>Następne kroki
Notesy są również szeroko używane podczas przygotowywania danych, wizualizacji danych, uczenia maszynowego i innych scenariuszy danych Big Data. Aby dowiedzieć się więcej na temat korzystania z notesów na potrzeby analizy danych i scenariuszy danych Big Data, odwiedź następujące samouczki:
  - [Tworzenie notesu](./spark/../../quickstart-apache-spark-notebook.md)
  - [Tworzenie wizualizacji przy użyciu notesów programu Synapse Studio](./spark/../apache-spark-data-visualization-tutorial.md)
  - [Kompiluj modele uczenia maszynowego za pomocą Apache Spark MLlib](./spark/../apache-spark-machine-learning-mllib-notebook.md)
  - [Tworzenie modeli uczenia maszynowego za pomocą zautomatyzowanej platformy Azure](./spark/../apache-spark-azure-machine-learning-tutorial.md)