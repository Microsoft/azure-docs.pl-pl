---
title: Konwertowanie zawartości certyfikatu na Base-64 — Azure HDInsight
description: Konwertowanie zawartości certyfikatu nazwy głównej usługi na format zakodowanego ciągu Base-64 w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 0ee2e783a7a9443db4c96817cf611272667c675a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944415"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Konwertowanie zawartości certyfikatu nazwy głównej usługi na format zakodowanego ciągu Base-64 w usłudze HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Zostanie wyświetlony komunikat o błędzie z informacją, że dane wejściowe nie są prawidłowym ciągiem Base-64, ponieważ zawiera znak inny niż podstawowy 64, więcej niż dwa znaki uzupełnienia lub znak niebędący odstępem między znakami uzupełniania.

## <a name="cause"></a>Przyczyna

Podczas używania programu PowerShell lub wdrożenia szablonu platformy Azure do tworzenia klastrów z Data Lake jako podstawowy lub dodatkowy magazyn, zawartość certyfikatu jednostki usługi podana w celu uzyskania dostępu do konta magazynu Data Lake ma format Base-64. Niewłaściwa konwersja zawartości certyfikatu pfx na ciąg zakodowany w formacie Base-64 może prowadzić do tego błędu.

## <a name="resolution"></a>Rozwiązanie

Gdy certyfikat główny usługi jest używany w formacie pfx (zobacz [tutaj](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) , aby zapoznać się z przykładowymi instrukcjami tworzenia głównej usługi), użyj następującego polecenia programu PowerShell lub fragmentu kodu w języku C#, aby przekonwertować zawartość certyfikatu na format base-64.

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]