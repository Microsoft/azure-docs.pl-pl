---
title: Typowe przyczyny odtwarzania ról usługi w chmurze (klasycznej) | Microsoft Docs
description: Rola usługi w chmurze, która nagle odtwarza może spowodować znaczny Przestój. Poniżej przedstawiono niektóre typowe problemy, które powodują odtworzenie ról, co może pomóc w zmniejszeniu przestojów.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 9610b32207f8367b9415c0881e49b54e24c49ad7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98741166"
---
# <a name="common-issues-that-cause-azure-cloud-service-classic-roles-to-recycle"></a>Typowe problemy, które powodują odtworzenie ról usługi w chmurze Azure (klasycznej)

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

W tym artykule omówiono niektóre typowe przyczyny problemów z wdrażaniem i przedstawiono wskazówki dotyczące rozwiązywania problemów, aby pomóc w rozwiązaniu tych problemów. Wskazanie, że problem występuje w przypadku aplikacji, gdy uruchomienie wystąpienia roli nie powiedzie się lub przechodzi między stanem inicjowania, zajętości i zatrzymywania.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Brak zależności środowiska uruchomieniowego
Jeśli rola w aplikacji korzysta z dowolnego zestawu, który nie jest częścią .NET Framework lub biblioteki zarządzanej platformy Azure, musisz jawnie dołączyć ten zestaw do pakietu aplikacji. Pamiętaj, że inne platformy firmy Microsoft nie są domyślnie dostępne na platformie Azure. Jeśli rola zależy od takiej struktury, należy dodać te zestawy do pakietu aplikacji.

Przed skompilowaniem i spakowaniem aplikacji sprawdź następujące kwestie:

* Jeśli używasz programu Visual Studio, upewnij się, że właściwość **copy Local** jest ustawiona na **wartość true** dla każdego przywoływanego zestawu w projekcie, który nie jest częścią zestawu Azure SDK lub .NET Framework.
* Upewnij się, że plik web.config nie odwołuje się do żadnych nieużywanych zestawów w elemencie compilation.
* **Akcja kompilacji** każdego pliku. cshtml ma ustawioną **zawartość**. Daje to pewność, że pliki będą prawidłowo wyświetlane w pakiecie i umożliwią wyświetlanie innych plików, do których istnieją odwołania w pakiecie.

## <a name="assembly-targets-wrong-platform"></a>Niewłaściwa platforma dla zestawu
Platforma Azure to środowisko 64-bitowe. W związku z tym zestawy .NET skompilowane dla elementu docelowego 32-bitowego nie będą działały na platformie Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Rola zgłasza Nieobsłużone wyjątki podczas inicjowania lub zatrzymywania
Wszystkie wyjątki, które są zgłaszane przez metody klasy [RoleEntryPoint] , które obejmują metody [OnStart], [OnStop]i [Run] , są Nieobsłużone wyjątki. Jeśli wystąpił nieobsługiwany wyjątek w jednej z tych metod, rola zostanie odtworzona. Jeśli rola jest odtwarzana wielokrotnie, może wystąpić nieobsłużony wyjątek przy każdym próbie uruchomienia.

## <a name="role-returns-from-run-method"></a>Rola zwraca z metody Run
Metoda [Run] jest przeznaczona do uruchamiania na czas nieokreślony. Jeśli kod zastępuje metodę [Run] , powinien on być uśpiony w nieskończoność. Jeśli metoda [Run] zwraca metodę, odtwarzana jest rola.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Nieprawidłowe ustawienie DiagnosticsConnectionString
Jeśli aplikacja używa Diagnostyka Azure, plik konfiguracji usługi musi określać `DiagnosticsConnectionString` ustawienie konfiguracji. To ustawienie powinno określać połączenie HTTPS z kontem magazynu na platformie Azure.

Aby upewnić się, że Twoje `DiagnosticsConnectionString` ustawienie jest poprawne przed wdrożeniem pakietu aplikacji na platformie Azure, sprawdź następujące kwestie:  

* `DiagnosticsConnectionString`Ustawienie wskazuje na prawidłowe konto magazynu na platformie Azure.  
  Domyślnie to ustawienie wskazuje na emulowane konto magazynu, dlatego należy jawnie zmienić to ustawienie przed wdrożeniem pakietu aplikacji. Jeśli to ustawienie nie zostanie zmienione, wyjątek jest zgłaszany, gdy wystąpienie roli podejmie próbę uruchomienia monitora diagnostycznego. Może to spowodować, że wystąpienie roli będzie odtwarzane w nieskończoność.
* Parametry połączenia są określone w następującym [formacie](../storage/common/storage-configure-connection-string.md). (Protokół musi być określony jako HTTPS). Zastąp ciąg moje *AccountName* nazwą konta magazynu i *MyAccountKey* z kluczem dostępu:    

```console
DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey
```

  Jeśli tworzysz aplikację przy użyciu narzędzi platformy Azure dla Microsoft Visual Studio, możesz użyć stron właściwości do ustawienia tej wartości.

## <a name="exported-certificate-does-not-include-private-key"></a>Wyeksportowany certyfikat nie zawiera klucza prywatnego
Aby uruchomić rolę sieci Web w ramach protokołu TLS, należy się upewnić, że wyeksportowany certyfikat zarządzania zawiera klucz prywatny. W przypadku eksportowania certyfikatu przy użyciu *Menedżera certyfikatów systemu Windows* należy wybrać opcję **tak** dla opcji **Eksportuj klucz prywatny** . Certyfikat musi zostać wyeksportowany w formacie PFX, który jest obecnie obsługiwany.

## <a name="next-steps"></a>Następne kroki
Zobacz więcej [artykułów do rozwiązywania problemów](../index.yml?product=cloud-services&tag=top-support-issue) dotyczących usług Cloud Services.

Zobacz więcej scenariuszy tworzenia ról na [seriach blogów Jan Williamson](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).

[RoleEntryPoint]: /previous-versions/azure/reference/ee758619(v=azure.100)
[OnStart]: /previous-versions/azure/reference/ee772851(v=azure.100)
[OnStop]: /previous-versions/azure/reference/ee772844(v=azure.100)
[Uruchom]: /previous-versions/azure/reference/ee772746(v=azure.100)