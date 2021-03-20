---
title: Właściwości metadanych zawartości
titleSuffix: Azure Cognitive Search
description: Właściwości metadanych dokumentów mogą dostarczać zawartość do pól w indeksie wyszukiwania lub informacje, które informuje o zachowaniu indeksowania w czasie wykonywania. W tym artykule wymieniono właściwości metadanych obsługiwane w usłudze Azure Wyszukiwanie poznawcze.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/22/2021
ms.openlocfilehash: cbb35f596a1d32816d1a73b462bf590d9dde0d52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668422"
---
# <a name="content-metadata-properties-used-in-azure-cognitive-search"></a>Właściwości metadanych zawartości używane w usłudze Azure Wyszukiwanie poznawcze

Usługa SharePoint Online i magazyn obiektów blob platformy Azure mogą zawierać różne zawartość, a wiele z nich ma właściwości metadanych, które mogą być przydatne do indeksowania. Podobnie jak można utworzyć pola wyszukiwania dla standardowych właściwości obiektów blob, takich jak **`metadata_storage_name`** , można utworzyć pola dla właściwości metadanych, które są specyficzne dla formatu dokumentu.

## <a name="supported-document-formats"></a>Obsługiwane formaty dokumentów

Wyszukiwanie poznawcze obsługuje indeksowanie obiektów blob i indeksowanie dokumentów usługi SharePoint Online dla następujących formatów dokumentu:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="properties-by-document-format"></a>Właściwości według formatu dokumentu

Poniższa tabela zawiera podsumowanie przetwarzania dla każdego formatu dokumentu oraz opis właściwości metadanych wyodrębnionych przez indeksator obiektów blob i indeksatora usługi SharePoint Online.

| Format dokumentu/typ zawartości | Wyodrębnione metadane | Szczegóły przetwarzania |
| --- | --- | --- |
| HTML (tekst/HTML) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Rozpakowywanie znacznika HTML i wyodrębnienie tekstu |
| PDF (aplikacja/PDF) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Wyodrębnij tekst, w tym osadzone dokumenty (z wyjątkiem obrazów) |
| DOCX (Application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnij tekst, w tym osadzone dokumenty |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnij tekst, w tym osadzone dokumenty |
| DOCM (Application/vnd.ms-word.document. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnij tekst, w tym osadzone dokumenty |
| WORD XML (application/vnd. MS-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Przepakowywanie znacznika XML i wyodrębnienie tekstu |
| WORD 2003 XML (application/vnd. MS-WordML) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Przepakowywanie znacznika XML i wyodrębnienie tekstu |
| XLSX (application/vnd. openxmlformats-officedocument. SpreadsheetML. Sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnij tekst, w tym osadzone dokumenty |
| XLS (application/vnd. MS-Excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnij tekst, w tym osadzone dokumenty |
| XLSM (application/vnd. MS-Excel. Sheet. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnij tekst, w tym osadzone dokumenty |
| PPTX (application/vnd. openxmlformats-officedocument. presentationml. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Wyodrębnij tekst, w tym osadzone dokumenty |
| PPT (application/vnd. MS-PowerPoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Wyodrębnij tekst, w tym osadzone dokumenty |
| PPTM (application/vnd. MS-PowerPoint. Presentation. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Wyodrębnij tekst, w tym osadzone dokumenty |
| MSG (application/vnd. MS-Outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Wyodrębnij tekst, w tym tekst wyodrębniony z załączników. `metadata_message_to_email``metadata_message_cc_email`i `metadata_message_bcc_email` są kolekcjami ciągów, pozostałe pola są ciągami.|
| ODT (application/vnd. języka Oasis. OpenDocument. Text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Wyodrębnij tekst, w tym osadzone dokumenty |
| ODS (application/vnd. języka Oasis. OpenDocument. arkusza kalkulacyjnego) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Wyodrębnij tekst, w tym osadzone dokumenty |
| ODP (application/vnd. języka Oasis. OpenDocument. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Wyodrębnij tekst, w tym osadzone dokumenty |
| Plik ZIP (Application/zip) |`metadata_content_type` |Wyodrębnij tekst ze wszystkich dokumentów w archiwum |
| GZ (aplikacja/gzip) |`metadata_content_type` |Wyodrębnij tekst ze wszystkich dokumentów w archiwum |
| EPUB (Application/EPUB + zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Wyodrębnij tekst ze wszystkich dokumentów w archiwum |
| XML (aplikacja/XML) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Przepakowywanie znacznika XML i wyodrębnienie tekstu |
| JSON (Application/JSON) |`metadata_content_type`<br/>`metadata_content_encoding` |Wyodrębnij tekst<br/>Uwaga: Jeśli chcesz wyodrębnić wiele pól dokumentu z obiektu BLOB JSON, zobacz [indeksowanie obiektów BLOB JSON](search-howto-index-json-blobs.md) w celu uzyskania szczegółów |
| EML (Message/RFC822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Wyodrębnij tekst, w tym załączniki |
| RTF (aplikacja/RTF) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Wyodrębnij tekst|
| Zwykły tekst (tekst/zwykły) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Wyodrębnij tekst|

## <a name="see-also"></a>Zobacz też

* [Indeksatory w usłudze Azure Cognitive Search](search-indexer-overview.md)
* [Zrozumienie obiektów BLOB przy użyciu AI](search-blob-ai-integration.md)
* [Omówienie indeksowania obiektów BLOB](search-blob-storage-integration.md)
* [Indeksowanie online usługi SharePoint](search-howto-index-sharepoint-online.md)