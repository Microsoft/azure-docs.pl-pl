---
title: Pobieranie obrazów z dronów
description: W tym artykule opisano sposób pobierania obrazów drona z partnerów.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 04f0c3c63d00ea49bf43f00f256266599a73d6c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88508808"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Pobieranie obrazów drona z partnerów drona

W tym artykule opisano, jak można przyorthomosaic dane z partnerów drona obrazów do usługi Azure FarmBeats Datahub. Orthomosaic to antenowa ilustracja lub obraz, który jest geometrycznie skorygowany i z danych zebranych przez drona.

Obecnie obsługiwane są następujące partnerzy obrazów.

  ![Partnerzy FarmBeats drona](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

Integrowanie danych drona obrazów z usługą Azure FarmBeats ułatwia uzyskiwanie danych orthomosaic z lotów drona prowadzonych w farmie w datahub. Gdy dane będą dostępne, możesz je wyświetlić w akceleratorze FarmBeats. Dane mogą służyć do łączenia się z danymi i tworzenia modeli w ramach modelu uczenia maszynowego.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

  - Upewnij się, że zainstalowano usługę Azure FarmBeats. Aby uzyskać informacje na temat sposobu instalowania programu FarmBeats, zobacz [Instalowanie platformy Azure FarmBeats](install-azure-farmbeats.md).
  - Upewnij się, że masz farmę, dla której chcesz utworzyć drona obrazów w systemie FarmBeats.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Włącz integrację obrazów drona z usługą FarmBeats

Podaj następujące informacje dla dostawcy urządzenia, aby włączyć integrację z usługą FarmBeats:
 - Punkt końcowy interfejsu API
 - Identyfikator dzierżawy
 - Identyfikator klienta
 - Klucz tajny klienta

Wykonaj następujące kroki.

1. Pobierz ten [skrypt](https://aka.ms/farmbeatspartnerscript)i wyodrębnij go na dysk lokalny. Dwa pliki znajdują się w pliku zip.
2. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/) i otwórz usługę Azure Cloud Shell. Ta opcja jest dostępna na pasku narzędzi w prawym górnym rogu portalu.

    ![Otwórz Azure Cloud Shell w prawym górnym rogu portalu](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Upewnij się, że środowisko jest ustawione na **PowerShell**.

    ![Ustawienie programu PowerShell](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Przekaż dwa pliki pobrane z kroku 1 w wystąpieniu Cloud Shell.

    ![Przekazywanie plików](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Przejdź do katalogu, do którego pliki zostały przekazane. Domyślnie są one przekazywane do katalogu macierzystego w ramach nazwy użytkownika.
6. Uruchom poniższy skrypt:

    ```azurepowershell-interactive

    ./generateCredentials.ps1

    ```

7. Postępuj zgodnie z instrukcjami na ekranie, aby przechwycić wartości punktu końcowego interfejsu API, identyfikatora dzierżawy, identyfikatora klienta, klucza tajnego klienta i parametrów połączenia centrum EventHub.

    Po wprowadzeniu wymaganych poświadczeń do systemu oprogramowania drona partnera można zaimportować wszystkie farmy z systemu FarmBeats. Następnie możesz użyć szczegółowych informacji o farmie, aby przeprowadzić planowanie ścieżki lotu i zbieranie obrazów drona.

    Po przetworzeniu nieprzetworzonych obrazów przez oprogramowanie dostawców drona, system oprogramowania drona przekazuje załączone orthomosaic i inne przetworzone obrazy do datahub.

## <a name="view-drone-imagery"></a>Wyświetlanie obrazów drona

Po wysłaniu danych do datahub FarmBeats można wysyłać zapytania do magazynu scen przy użyciu interfejsów API FarmBeats Datahub.

Możesz też wyświetlić najnowszy obraz drona na stronie **szczegółów farmy** . Aby wyświetlić obraz, wykonaj kroki opisane w sekcji.

1. Wybierz farmę, do której został przekazany obraz. Zostanie wyświetlona strona szczegóły **farmy** .
2. Przewiń w dół do sekcji Najnowsze **mapy dokładności** .
3. Zapoznaj się z obrazem w sekcji **obrazy drona** .

    ![Sekcja drona obrazów](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Pobieranie obrazów drona

Po wybraniu sekcji drona obrazy zostanie otwarte okno podręczne pokazujące obraz o wysokiej rozdzielczości drona orthomosaic.

![Orthomosaic o wysokiej rozdzielczości](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Wyświetl wszystkie mapy drona

Pliki i obrazy przekazane przez dostawcę drona są wyświetlane w sekcji **Maps** . Wybierz sekcję **Maps** , Filtruj według **farmy**i wybierz odpowiednie pliki do wyświetlenia i pobrania.

  ![Sekcja Maps](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak używać [interfejsów API](rest-api-in-azure-farmbeats.md) FarmBeats Datahub, aby uzyskać obraz drona.
