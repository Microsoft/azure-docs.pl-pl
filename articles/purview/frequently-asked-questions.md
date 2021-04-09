---
title: Często zadawane pytania
description: W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące usługi Azure kontrolą.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 94b765cbcbdd81505b08052845207ee1d93a28d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667811"
---
# <a name="frequently-asked-questions-faq-about-azure-purview"></a>Często zadawane pytania dotyczące platformy Azure kontrolą

## <a name="overview"></a>Omówienie

Wiele organizacji nie ma całościowego wglądu w dane. Nie można zrozumieć, jakie dane istnieją, gdzie znajdują się dane oraz jak znaleźć odpowiednie dane i uzyskać do nich dostęp. Dane nie mają kontekstu, takiego jak pochodzenie, klasyfikacja i kompleksowe metadane, co utrudnia użytkownikom biznesowym wyszukiwanie odpowiednich danych i odpowiednie korzystanie z tych danych. W związku z tym tylko niewielka część zebranych danych jest używana do informowania o decyzjach handlowych. Na koniec identyfikowanie problemów z bezpieczeństwem danych i ochrona poufnych danych jest niespójna. Wymaga to ciągłego czasu i nakładu pracy, szczególnie przy zachowaniu elastyczności danych.

Azure kontrolą to rozwiązanie do zarządzania danymi. Ułatwia ona klientom uzyskanie dokładnej wiedzy na temat wszystkich danych przy zachowaniu kontroli nad jej użyciem. Dzięki usłudze Azure kontrolą organizacje odkrywają i nadzorują dane. Uzyskują wgląd w swoje dane i centralnie regulują dostęp do danych.

## <a name="purpose-of-this-faq"></a>Cel tych często zadawanych pytań

Często zadawane pytania odpowiadają typowym pytaniam, które często pytają klienci i zespoły pól. Ma ona na celu wyjaśnienie pytań dotyczących usługi Azure kontrolą i powiązanych rozwiązań, takich jak Azure Data Catalog (ADC) gen 2 (przestarzałe) i Azure Information Protection.

### <a name="what-are-the-source-types-available-for-metadata-scanning-and-classification"></a>Jakie typy źródeł są dostępne do skanowania i klasyfikowania metadanych?

|Azure|Poza platformą Azure|
|---------|---------|
|Azure Blob Storage|Power BI|
|Azure Synapse Analytics (SQL DW)|SQL Server |
|Azure Cosmos DB|Teradata (dostępne z końcem 2020)|
|Wystąpienie zarządzane Azure SQL|SAP ECC (dostępne na końcu 2020)|
|Azure Data Explorer|SAP S/4 HANA (dostępne z końcem 2020)|
|Usługa Azure Data Lake Storage 1. generacji|Magazyn metadanych Hive (dostępny z końcem 2020)|
|Usługa Azure Data Lake Storage 2. generacji|Amazon S3|
|Azure Files|--|
|Azure SQL Database|--|

### <a name="what-data-systemsprocessors-can-we-connect-and-get-lineage"></a>Jakie systemy danych/procesory można połączyć i pobrać z nich?

|System/procesor danych 
|---------
|Azure Data Factory: działanie kopiowania, działanie przepływu danych 
|Niestandardowa pochodzenie   
|Azure Data Share   
|Power BI    |
|Usługi SQL Server Integration Services  

### <a name="how-are-adc-gen-2-azure-information-protection-and-azure-purview-related"></a>Jak są powiązane z programem ADC Gen 2, Azure Information Protection i Azure kontrolą?

Usługa Azure kontrolą rozpoczęła się pierwotnie jako ADC Gen 2, ale została rozszerzona o zakres. Obejmuje ona teraz zaawansowane możliwości katalogu ADC Gen 2 połączone z możliwościami klasyfikacji danych, etykietowania i wymuszania zasad zgodności Azure Information Protection. Obecnie jest to bardziej ściśle dostosowane do szerszej definicji branży zarządzania danymi.

### <a name="what-happens-to-customers-using-adc-gen-1"></a>Co się dzieje z klientami przy użyciu narzędzia ADC Gen 1?

Usługa Azure kontrolą to skoncentrowanie się na wszystkich innowacyjności produktu w obszarze rozwiązań katalogu dla firmy Microsoft. Program ADC Gen 1 będzie nadal obsługiwany.

### <a name="can-customers-have-multiple-azure-purview-accounts-in-the-same-subscription"></a>Czy klienci mogą korzystać z wielu kont usługi Azure kontrolą w ramach tej samej subskrypcji?

Tak. Obsługujemy wiele kont usługi Azure kontrolą na subskrypcję i na dzierżawcę.

### <a name="can-i-run-adc-gen-1-and-azure-purview-in-parallel"></a>Czy można równolegle uruchamiać ADC Gen 1 i Azure kontrolą?

Tak. Obie są niezależne usługi.

### <a name="how-do-i-migrate-existing-adc-gen-1-data-assets-to-azure-purview"></a>Jak mogę zmigrować istniejące zasoby danych programu ADC Gen 1 do usługi Azure kontrolą?

Użyj interfejsów API usługi Azure kontrolą, aby wyodrębnić z programu ADC Gen 1 i pozyskania do usługi Azure kontrolą. W słowniku są obsługiwane narzędzia zbiorcze oparte na woluminach CSV.

### <a name="how-do-i-encrypt-sensitive-data-for-sql-tables-using-azure-purview"></a>Jak mogę szyfrować poufne dane dla tabel SQL za pomocą usługi Azure kontrolą?

Szyfrowanie danych odbywa się na poziomie źródła danych. Usługa Azure kontrolą przechowuje tylko metadane. Nie wyświetla podgląd danych.

### <a name="will-all-the-capabilities-of-adc-gen-2-exist-in-azure-purview"></a>Czy wszystkie możliwości programu ADC Gen 2 istnieją w usłudze Azure kontrolą?

Tak.

<!--## Is the data lineage feature available in Azure Purview?

Yes, but it's limited to the Azure Data Factory connector.

<!-- ## How can I scan SQL Server on-premises? 

Use the self-host integration runtime capability. !-->

<!--### What is the difference between classification in Azure SQL Database and classification in Azure Purview?

|Azure SQL DB classification  |Azure Purview classification  |
|---------|---------|
|Classification is based on SQL metadata from system catalogs. |Classification is based on Azure Purview's sampling technique by using the system-defined or custom-defined regex pattern.|
|Custom classification is supported.     |Custom classification is supported.         |
|Doesn't use Microsoft 365 system classifiers out of the box.    | Uses Microsoft 365 system classifiers out of the box.        |
-->

### <a name="whats-the-difference-between-a-glossary-and-classification"></a>Jaka jest różnica między słownikiem a klasyfikacją?

W słowniku użyto konwencji nazewnictwa, a następnie użytkowników nietechnicznych/firmowych danych, znanych także jako konsumenci danych. Te typy osób są analitykami biznesowymi lub naukowcami danych, którzy korzystają z usługi Azure kontrolą w celu wyszukiwania określonych typów danych na podstawie użycia biznesowego. Na przykład analityki łańcucha dostaw mogą wymagać wyszukania *typów jednostek SKU* i *szczegółów wysyłki*. Przeszukuje słownik pod kątem tych warunków, aby znaleźć odpowiednie dane.
Klasyfikacja jest tagiem zastosowanym do zasobu danych na poziomie tabeli, kolumny lub pliku, który identyfikuje, jakie dane istnieją w elemencie zawartości. Klasyfikację można zastosować automatycznie lub ręcznie na podstawie typu znalezionych danych. Zwykle znaczniki klasyfikacji są używane do identyfikowania, czy zasób zawiera poufne dane, oraz rodzaju poufnych danych, które mogą być.

### <a name="does-azure-purview-scan-and-classify-emails-pdfs-etc-in-my-sharepoint-and-onedrive"></a>Czy usługa Azure kontrolą skanuje i klasyfikuje wiadomości e-mail, pliki PDF itp. w moich programach SharePoint i OneDrive?

Skanowanie w poszukiwaniu lokalnych witryn i bibliotek programu SharePoint jest dostępne za pomocą skanera Azure Information Protection. Skaner jest dostępny do użycia w ramach subskrypcji Microsoft 365 klienta z następującymi jednostkami SKU: w przypadku platformy, EMS E3 i M365 E3. Jeśli masz jedną z tych jednostek SKU, musisz mieć odpowiednie uprawnienia do rozpoczęcia korzystania ze skanera Azure Information Protection.

<!--### What is the difference between classifications and sensitivity labels in Azure Purview?

Azure Purview's data governance solution is based on the Apache Atlas framework. As defined by Atlas, classification is a way to identify the contents of an asset (table or file) or an entity (table column or structured file). This classification becomes a metadata property that allows Azure Purview to understand the data within each asset and govern and protect them.

Sensitivity labels are a Microsoft 365 concept that resembles classification at the asset level. You create a label with a collection of classifications applied at the asset or entity level.

Atlas-centric customers will see no real distinction between classifications and labels. To these customers, everything is a classification and labels aren't needed.

Security-focused customers will see a distinction between classification and labeling, but only because in Microsoft 365 the classifications aren't exposed directly to the user; only labels are visible. So, similar to Atlas, Office 365 security customers don't need to deal with both entities.
-->

### <a name="what-is-the-compute-used-for-the-scan"></a>Jakie są obliczenia używane podczas skanowania?
Istnieje infrastruktura skanowania zarządzana przez firmę Microsoft. W przypadku większości obsługiwanych zasobów platformy Azure/AWS nie trzeba wdrażać infrastruktury skanowania.

### <a name="is-there-a-way-to-provision-azure-purview-via-azure-resource-manager-arm-template--cli--powershell"></a>Czy istnieje sposób udostępniania usługi Azure kontrolą za pośrednictwem szablonu Azure Resource Manager (ARM)/interfejsu wiersza polecenia/PowerShell?

Tak, szablon ARM jest dostępny

<!--### Does Azure Purview support guest users in AAD?-->

### <a name="im-already-using-atlas-can-i-easily-move-to-azure-purview"></a>Jestem już w trakcie korzystania z usługi Azure kontrolą?

Usługa Azure kontrolą jest zgodna z interfejsem API w języku Atlas. W przypadku migrowania z usługi Atlas zaleca się najpierw skanować źródła danych przy użyciu kontrolą Azure. Gdy zasoby są dostępne na Twoim koncie, można użyć podobnych interfejsów API do integracji, takich jak aktualizowanie zasobów lub Dodawanie niestandardowych elementów zależnych. Usługa Azure kontrolą modyfikuje interfejs API wyszukiwania, tak aby używał Azure Search tak, aby móc korzystać z wyszukiwania z wyprzedzeniem.

### <a name="can-i-create-multiple-catalogs-in-my-tenant"></a>Czy mogę utworzyć wiele wykazów w mojej dzierżawie?

Tak, możesz utworzyć wiele kont usługi Azure kontrolą na subskrypcję i na dzierżawcę. Na stronie limity można przeglądać [i zwiększać przydziały dla zasobów przy użyciu usługi Azure kontrolą](how-to-manage-quotas.md).

Dodatkowe zalecenia dotyczące sytuacji, w których należy lub nie powinno mieć wielu kont, zostały udokumentowane w naszych [najlepszych rozwiązaniach dotyczących wdrażania usługi Azure kontrolą](deployment-best-practices.md).

### <a name="can-i-register-multiple-tenants-within-a-single-azure-purview-account"></a>Czy mogę zarejestrować wiele dzierżawców na jednym koncie usługi Azure kontrolą?

Nie, obecnie w celu skanowania źródła danych innej dzierżawy należy utworzyć osobne konto usługi Azure kontrolą w tej dzierżawie.

### <a name="does-azure-purview-support-column-level-lineage"></a>Czy usługa Azure kontrolą obsługuje zakres kolumn na poziomie kolumny?

Tak, usługa Azure kontrolą obsługuje elementy powiązane na poziomie kolumny.