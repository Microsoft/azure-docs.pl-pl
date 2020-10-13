---
title: Ustawienia wstępne zadania dla Azure Media Indexer
description: Ten temat zawiera omówienie ustawień wstępnych zadań dla Azure Media Services Media Indexer.
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: f8daa25239b935a9e0092c6bf2e388c7cc3c6789
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89264763"
---
# <a name="task-preset-for-azure-media-indexer"></a>Ustawienia wstępne zadania dla Azure Media Indexer

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Indexer jest procesorem multimedialnym, który służy do wykonywania następujących zadań: udostępnianie plików multimedialnych i zawartości, generowanie ścieżek i słów kluczowych napisów oraz plików zasobów indeksu, które są częścią elementu zawartości.

W tym temacie opisano ustawienia wstępne zadania, które należy przekazać do zadania indeksowania. Aby zapoznać się z pełnymi przykładami, zobacz [indeksowanie plików multimedialnych za pomocą Azure Media Indexer](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>Plik XML konfiguracji Azure Media Indexer

W poniższej tabeli objaśniono elementy i atrybuty pliku XML konfiguracji.

|Nazwa|Wymagane|Opis|
|---|---|---|
|Dane wejściowe|true|Pliki zasobów, które mają być indeksowane.<br/>Azure Media Indexer obsługuje następujące formaty plików multimedialnych: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Można określić nazwy plików w atrybucie **nazwy** lub **listy** elementu **wejściowego** (jak pokazano poniżej). Jeśli nie określisz pliku zasobów do indeksowania, plik podstawowy jest wybierany. Jeśli nie ustawiono podstawowego pliku zasobów, pierwszy plik w wejściowym elemencie zawartości jest indeksowany.<br/><br/>Aby jawnie określić nazwę pliku zasobu, wykonaj następujące czynności:<br/>```<input name="TestFile.wmv" />```<br/><br/>Można również indeksować wiele plików zasobów jednocześnie (do 10 plików). W tym celu:<br/>— Utwórz plik tekstowy (plik manifestu) i nadaj mu rozszerzenie. lst.<br/>-Dodaj listę wszystkich nazw plików zasobów w danych wejściowych do tego pliku manifestu.<br/>-Dodaj (Przekaż) plik manifestu do elementu zawartości.<br/>-Określ nazwę pliku manifestu w atrybucie listy danych wejściowych.<br/>```<input list="input.lst">```<br/><br/>**Uwaga:** Jeśli dodasz więcej niż 10 plików do pliku manifestu, zadanie indeksowania zakończy się niepowodzeniem z kodem błędu 2006.|
|metadane|fałsz|Metadane dla określonych plików zasobów.<br/>```<metadata key="..." value="..." />```<br/><br/>Można podać wartości wstępnie zdefiniowanych kluczy. <br/><br/>Obecnie obsługiwane są następujące klucze:<br/><br/>**title** i **Description** — służy do aktualizowania modelu języka w celu zwiększenia dokładności rozpoznawania mowy.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**Nazwa użytkownika** i **hasło** — używane do uwierzytelniania podczas pobierania plików internetowych za pośrednictwem protokołu HTTP lub https.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>Wartości Nazwa użytkownika i hasło są stosowane do wszystkich adresów URL multimediów w manifeście wejściowym.|
|funkcje<br/><br/>Dodano w wersji 1,2. Obecnie jedyną obsługiwaną funkcją jest rozpoznawanie mowy ("ASR").|fałsz|Funkcja rozpoznawania mowy ma następujące klucze ustawień:<br/><br/>Język:<br/>— Język naturalny, który ma zostać rozpoznany w pliku multimedialnym.<br/>— Angielski, hiszpański<br/><br/>CaptionFormats:<br/>-rozdzielana średnikami lista formatów żądanych napisów wyjściowych (jeśli istnieją)<br/>-ttml; WebVTT<br/><br/><br/>GenerateKeywords:<br/>-Wartość logiczna określająca, czy plik XML jest wymagany.<br/>Oznacza False.|

## <a name="azure-media-indexer-configuration-xml-example"></a>Przykład pliku XML konfiguracji Azure Media Indexer

``` 
<?xml version="1.0" encoding="utf-8"?>  
<configuration version="2.0">  
  <input>  
    <metadata key="title" value="[Title of the media file]" />  
    <metadata key="description" value="[Description of the media file]" />  
  </input>  
  <settings>  
  </settings>  
  
  <features>  
    <feature name="ASR">    
      <settings>  
        <add key="Language" value="English"/>  
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>Następne kroki

Zobacz [indeksowanie plików multimedialnych za pomocą Azure Media Indexer](media-services-index-content.md).

