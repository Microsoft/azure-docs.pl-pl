---
title: Konwertowanie zawartości certyfikatu na Base-64 — Azure HDInsight
description: Konwertowanie zawartości certyfikatu nazwy głównej usługi na format zakodowanego ciągu Base-64 w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c49957eeab921c86b9901461dc5da594fa4188f
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546963"
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

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się za pomocą [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).