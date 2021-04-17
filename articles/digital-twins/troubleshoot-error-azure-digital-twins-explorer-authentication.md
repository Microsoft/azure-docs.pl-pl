---
title: Azure Digital Twins Explorer błąd uwierzytelniania
description: Przyczyny i rozwiązania problemu "Uwierzytelnianie nie powiodło się". w Azure Digital Twins Explorer.
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/8/2021
ms.openlocfilehash: 1f8373130fbead2204dd0ac2515595d68dd3b2e8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495085"
---
# <a name="authentication-failed"></a>Uwierzytelnianie nie powiodło się

W tym artykule opisano przyczyny i kroki rozwiązywania problemu z komunikatem o błędzie ["Uwierzytelnianie](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) nie powiodło się" podczas uruchamiania przykładu Azure Digital Twins Explorer na komputerze lokalnym. 

## <a name="symptoms"></a>Objawy

Podczas konfigurowania i uruchamiania aplikacji Azure Digital Twins Explorer próby uwierzytelnienia w aplikacji są spełnione z następującym komunikatem o błędzie:

:::image type="content" source="media/troubleshoot-error-azure-digital-twins-explorer-authentication/authentication-error.png" alt-text="Zrzut ekranu przedstawiający komunikat o błędzie w Azure Digital Twins z następującym tekstem: Uwierzytelnianie nie powiodło się. Jeśli uruchamiasz aplikację lokalnie, upewnij się, że zalogowano się na platformie Azure na maszynie hosta lub na przykład przez uruchomienie polecenia &quot;az login&quot; w wierszu polecenia, przez zalogowanie się do programu Visual Studio lub VS Code albo przez ustawienie zmiennych środowiskowych. Jeśli potrzebujesz więcej informacji, zobacz readme lub poszukaj wartości DefaultAzureCredential w dokumentacji azure.Identity. Jeśli używasz narzędzia adt-explorer hostowanej w chmurze, upewnij się, że hostowana funkcja platformy Azure ma przypisaną przez system tożsamość zarządzaną. Zobacz readme, aby uzyskać więcej informacji.":::

## <a name="causes"></a>Przyczyny

### <a name="cause-1"></a>Przyczyna #1

Aplikacja Azure Digital Twins Explorer używa [defaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (część biblioteki), która będzie wyszukiwać poświadczenia `Azure.Identity` w środowisku lokalnym.

Jak podano w tekście błędu, ten błąd może wystąpić, jeśli nie podano poświadczeń lokalnych dla do `DefaultAzureCredential` pobrania.

Aby uzyskać więcej informacji na temat używania poświadczeń lokalnych z usługą Azure Digital Twins Explorer, zobacz sekcję Konfigurowanie lokalnych poświadczeń platformy [*Azure*](quickstart-adt-explorer.md#set-up-local-azure-credentials) w przewodniku Szybki start *Azure Digital Twins: eksplorowanie* przykładowego scenariusza.

### <a name="cause-2"></a>Przyczyna #2

Ten błąd może również wystąpić, jeśli konto platformy Azure nie ma wymaganych uprawnień kontroli dostępu na podstawie ról (RBAC) platformy Azure ustawionych w wystąpieniu Azure Digital Twins roli. Aby uzyskać dostęp do danych w wystąpieniu, musisz mieć rolę czytelnika danych usługi **Azure Digital Twins** lub właściciela danych usługi **Azure Digital Twins** odpowiednio w wystąpieniu, które próbujesz odczytać lub zarządzać. 

Aby uzyskać więcej informacji na temat zabezpieczeń i ról w Azure Digital Twins, zobacz [*Concepts: Security for Azure Digital Twins solutions (Pojęcia:*](concepts-security.md)zabezpieczenia Azure Digital Twins rozwiązań).

## <a name="solutions"></a>Rozwiązania

### <a name="solution-1"></a>Rozwiązanie #1

Najpierw upewnij się, że do aplikacji zostały podane niezbędne poświadczenia.

#### <a name="provide-local-credentials"></a>Podaj poświadczenia lokalne

`DefaultAzureCredential` usługa uwierzytelnia się przy użyciu informacji z lokalnego logowania na platformie Azure. Poświadczenia platformy Azure możesz podać, logując się do konta platformy Azure w lokalnym oknie interfejsu wiersza polecenia platformy [Azure](/cli/azure/install-azure-cli) lub Visual Studio lub Visual Studio Code.

Typy poświadczeń, które akceptują, a także kolejność ich prób, można wyświetlić w dokumentacji tożsamości platformy Azure dla wartości `DefaultAzureCredential` [DefaultAzureCredential.](/dotnet/api/overview/azure/identity-readme#defaultazurecredential)

Jeśli już zalogowano się lokalnie na właściwe konto platformy Azure i problem nie został rozwiązany, przejdź do następnego rozwiązania.

### <a name="solution-2"></a>Rozwiązanie #2

Sprawdź, czy użytkownik platformy Azure ma rolę czytelnika danych usługi **Azure Digital Twins** w wystąpieniu usługi Azure Digital Twins, jeśli próbujesz tylko odczytać jego dane, lub rolę właściciela danych usługi **Azure Digital Twins** w wystąpieniu, jeśli próbujesz zarządzać jego danymi.

Pamiętaj, że ta rola różni się od...
* wcześniejsza nazwa dla tej roli w wersji zapoznawczej, *Azure Digital Twins właściciel (wersja zapoznawcza) (rola* jest taka sama, ale nazwa została zmieniona)
* rolę *właściciela* w całej subskrypcji platformy Azure. *Azure Digital Twins data owner* jest rolą w Azure Digital Twins i jest ograniczony do tego Azure Digital Twins wystąpienia.
* rola *właściciela* w Azure Digital Twins. Są to dwie odrębne Azure Digital Twins zarządzania, a *Azure Digital Twins* właściciel danych to rola, która powinna być używana do zarządzania.

 Jeśli nie masz tej roli, skonfiguruj ją w celu rozwiązania problemu.

#### <a name="check-current-setup"></a>Sprawdzanie bieżącej konfiguracji

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Rozwiązywanie problemów 

Jeśli nie masz tego przypisania roli, ktoś z rolą właściciela w subskrypcji platformy **Azure** powinien uruchomić następujące polecenie, aby nadać użytkownikowi platformy Azure odpowiednią rolę w **wystąpieniu Azure Digital Twins azure.** 

Jeśli jesteś właścicielem subskrypcji, możesz uruchomić to polecenie samodzielnie. Jeśli nie, skontaktuj się z właścicielem, aby uruchomić to polecenie w Twoim imieniu. Nazwa roli jest nazwą właściciela Azure Digital Twins **do** edycji lub Azure Digital Twins **danych** w celu uzyskania dostępu do odczytu.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "<role-name>"
```

Aby uzyskać więcej informacji na temat tego [](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) wymagania roli i procesu przypisywania, zobacz sekcję Konfigurowanie uprawnień dostępu użytkownika w sekcji Uwierzytelnianie i konfigurowanie wystąpienia oraz uwierzytelniania (interfejs wiersza polecenia lub *portal).*

## <a name="next-steps"></a>Następne kroki

Przeczytaj kroki konfiguracji dotyczące tworzenia i uwierzytelniania nowego Azure Digital Twins wystąpienia:
* [*Uwierzytelnianie i konfigurowanie wystąpienia (interfejs wiersza polecenia)*](how-to-set-up-instance-cli.md)

Dowiedz się więcej o zabezpieczeniach i uprawnieniach Azure Digital Twins:
* [*Pojęcia: Zabezpieczenia dla Azure Digital Twins rozwiązań*](concepts-security.md)
