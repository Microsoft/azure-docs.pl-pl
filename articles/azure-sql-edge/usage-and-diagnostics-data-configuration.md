---
title: Konfiguracja danych dotyczących użycia i diagnostyki usługi Azure SQL Edge
description: Dowiedz się, jak skonfigurować dane dotyczące użycia i diagnostyki w usłudze Azure SQL Edge.
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 08/04/2020
ms.openlocfilehash: 81b2aabbae148faec06c9ab420bdfecde475b4bb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97605009"
---
# <a name="azure-sql-edge-usage-and-diagnostics-data-configuration"></a>Konfiguracja danych dotyczących użycia i diagnostyki usługi Azure SQL Edge

Domyślnie usługa Azure SQL Edge zbiera informacje o tym, jak klienci korzystają z aplikacji. W przypadku usługi Azure SQL Edge zbierane są informacje o doświadczeniu wdrażania, użyciu i wydajności. Te informacje ułatwiają firmie Microsoft ulepszanie produktu w celu lepszego zaspokajania potrzeb klientów. Na przykład firma Microsoft zbiera informacje o rodzaju kodów błędów napotykanych przez klientów, aby można było naprawić powiązane usterki, ulepszyć naszą dokumentację dotyczącą korzystania z usługi Azure SQL Edge i określić, czy funkcje powinny być dodawane do produktu w celu lepszego obsłużenia klientów.

W przeciwnym razie Firma Microsoft nie wysyła żadnych następujących typów informacji za pomocą tego mechanizmu:

- Wszystkie wartości z wewnątrz tabel użytkownika.
- Wszystkie poświadczenia logowania lub inne informacje o uwierzytelnianiu.
- Wszystkie dane Personal lub Customer.

Następujący przykładowy scenariusz zawiera informacje o użyciu funkcji, które ułatwiają ulepszanie produktu.

Poniżej przedstawiono przykładowe zapytanie z zapytań używanych na potrzeby zbierania danych dotyczących użycia i diagnostyki. Zapytanie określa liczbę i typy różnych źródeł danych przesyłania strumieniowego używanych w usłudze Azure SQL Edge. Te dane pomagają firmie Microsoft identyfikować, które źródła danych przesyłania strumieniowego są zwykle używane w taki sposób, aby firma Microsoft mogła ulepszyć wydajność i środowisko użytkownika związane z tymi źródłami danych. 

```sql
select 
count(*) as [count], sum(inputs) as inputs, sum(outputs) as outputs, sum(linked_to_job) 
as linked_to_job, data_source_type
from ( 
select isnull(value,'unknown') as data_source_type, inputs, outputs, linked_to_job
from 
    ( 
        select 
        convert(sysname, lower(substring(ds.location, 0, charindex('://', ds.location))), 1) as data_source_type, 
        isnull(inputs, 0) as inputs, isnull(outputs, 0) as outputs, isnull(js.stream_id/js.stream_id, 0) as linked_to_job 
        from sys.external_streams es
        join sys.external_data_sources ds 
             on es.data_source_id = ds.data_source_id
        left join 
            ( 
            select stream_id, max(cast(is_input as int)) inputs, max(cast(is_output as int)) outputs 
            from sys.external_job_streams group by stream_id 
            ) js
             on js.stream_id = es.object_id 
    ) ds
left join 
    (
        select value from string_split('edgehub,sqlserver,kafka', ',')) as known_ep on data_source_type = value 
    ) known_ds
group by data_source_type
```

## <a name="disable-usage-and-diagnostic-data-collection"></a>Wyłącz zbieranie danych użycia i diagnostyki

Zbieranie danych użycia i diagnostyki w usłudze Azure SQL Edge można wyłączyć przy użyciu jednej z poniższych metod.

> [!NOTE]
> Nie można wyłączyć danych użycia i diagnostyki dla wersji dla deweloperów.

### <a name="disable-usage-and-diagnostics-using-environment-variables"></a>Wyłączanie użycia i diagnostyki przy użyciu zmiennych środowiskowych

Aby wyłączyć zbieranie danych użycia i diagnostyki w usłudze Azure SQL Edge, Dodaj następującą zmienną środowiskową i ustaw jej wartość na `*False*` . Aby uzyskać więcej informacji na temat konfigurowania usługi Azure SQL Edge przy użyciu zmiennych środowiskowych, zobacz [Konfigurowanie przy użyciu zmiennych środowiskowych](configure.md#configure-by-using-environment-variables).

`MSSQL_TELEMETRY_ENABLED = TRUE | FALSE`

- TRUE — umożliwia zbieranie danych dotyczących użycia i diagnostyki. To jest konfiguracja domyślna.
- FALSE — wyłącza zbieranie danych dotyczących użycia i diagnostyki.

### <a name="disable-usage-and-diagnostics-using-mssqlconf-file"></a>Wyłącz użycie i diagnostykę przy użyciu pliku MSSQL. conf

Aby wyłączyć zbieranie danych użycia i diagnostyki w usłudze Azure SQL Edge, Dodaj następujące wiersze w pliku MSSQL. conf na dysku trwałym, który jest mapowany do folderu/var/opt/MSSQL/w module Microsoft Edge. Aby uzyskać więcej informacji na temat konfigurowania usługi Azure SQL Edge przy użyciu pliku MSSQL. conf, zapoznaj się z tematem [Konfigurowanie przy użyciu pliku MSSQL. conf](configure.md#configure-by-using-an-mssqlconf-file).

```ini
[telemetry]
customerfeedback = false
```

## <a name="local-audit-of-usage-and-diagnostic-data-collection"></a>Lokalna Inspekcja zbierania danych użycia i diagnostyki

Składnik Inspekcja lokalna usługi Azure SQL Edge użycie i zbieranie danych diagnostycznych mogą zapisywać dane zebrane przez usługę w wydzielonym folderze, reprezentujące dane (dzienniki), które zostaną wysłane do firmy Microsoft. Celem lokalnego audytu jest umożliwienie klientom wyświetlenia wszystkich danych zbieranych przez firmę Microsoft za pomocą tej funkcji, w celu zapewnienia zgodności z przepisami lub weryfikacji prywatności.

### <a name="enable-local-audit-of-usage-and-diagnostics-data"></a>Włącz lokalną inspekcję danych użycia i diagnostyki

Aby włączyć lokalne inspekcje danych użycia i diagnostyki w usłudze Azure SQL Edge

1. Utwórz katalog docelowy dla nowego lokalnego magazynu dziennika inspekcji. Ten katalog docelowy może znajdować się na hoście lub w kontenerze. W poniższym przykładzie katalog docelowy jest tworzony w tym samym woluminie instalacji, który jest zamapowany na ścieżkę/var/opt/MSSQL/w usłudze SQL Edge.

   ```bash
   sudo mkdir <host mount path>/audit
   ```

2. Skonfiguruj inspekcję danych użycia i diagnostyki przy użyciu zmiennych środowiskowych lub pliku MSSQL. conf.

   - Przy użyciu zmiennych środowiskowych — Dodaj następującą zmienną środowiskową do wdrożenia programu SQL Edge i określ katalog docelowy dla plików inspekcji.
   
     `*MSSQL_TELEMETRY_DIR = <host mount path>/audit*`
   
   - Przy użyciu pliku MSSQL. conf Dodaj następujące wiersze w pliku MSSQL. conf i określ katalog docelowy dla plików inspekcji.
       ```ini
       [telemetry]
       userrequestedlocalauditdirectory  = <host mount path>/audit
       ```  

## <a name="next-steps"></a>Następne kroki

- [Łączenie z usługą Azure SQL Edge](connect.md)
- [Tworzenie kompleksowego rozwiązania IoT przy użyciu usługi SQL Edge](tutorial-deploy-azure-resources.md)
