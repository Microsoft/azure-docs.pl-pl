---
title: 'Samouczek: Tworzenie rekordu aliasu w celu odwoływania się do rekordu zasobu w strefie'
titleSuffix: Azure DNS
description: W tym samouczku przedstawiono, jak skonfigurować rekord aliasu usługi Azure DNS do odwoływania do rekordu zasobów w strefie DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: rohink
ms.openlocfilehash: 2b122a34cfd382a58f7680743d3a1cb1ae598fd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "76939248"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>Samouczek: tworzenie rekordu aliasu do odwoływania do rekordu zasobów w strefie DNS

Rekordy aliasów mogą odwoływać się do innych zestawów rekordów tego samego typu. Na przykład zestaw rekordów DNS CNAME może być aliasem dla innego zestawu rekordów CNAME tego samego typu. Ta funkcja jest przydatna, jeśli chcesz, by z punktu widzenia zachowania niektóre zestawy rekordów były aliasami, a niektóre nie.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie rekordu aliasu dla rekordu zasobów w strefie DNS.
> * Testowanie rekordu aliasu.


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne
Do testowania niezbędna jest nazwa domeny, którą można hostować w usłudze Azure DNS. Musisz mieć pełną kontrolę nad tą domeną. Pełna kontrola obejmuje możliwość ustawiania dla domeny rekordów serwera nazw (NS).

Aby uzyskać instrukcje dotyczące hostowania własnej domeny w usłudze Azure DNS, zobacz [Samouczek: hostowanie własnej domeny w usłudze Azure DNS](dns-delegate-domain-azure-dns.md).


## <a name="create-an-alias-record"></a>Tworzenie rekordu aliasu

Utwórz rekord aliasu, który wskazuje na rekord zasobów w strefie DNS.

### <a name="create-the-target-resource-record"></a>Tworzenie docelowego rekordu zasobów
1. Wybierz strefę usługi Azure DNS, aby ją otworzyć.
2. Wybierz pozycję **Zestaw rekordów**.
3. W polu tekstowym **Nazwa** wprowadź **serwer**.
4. Dla opcji **Typ** wybierz pozycję **A**.
5. W polu tekstowym **ADRES IP** wprowadź **10.10.10.10**.
6. Wybierz przycisk **OK**.

### <a name="create-the-alias-record"></a>Tworzenie rekordu aliasu
1. Wybierz strefę usługi Azure DNS, aby ją otworzyć.
2. Wybierz pozycję **Zestaw rekordów**.
3. W polu tekstowym **Nazwa** wprowadź **test**.
4. Dla opcji **Typ** wybierz pozycję **A**.
5. Zaznacz pozycję **Tak** w polu wyboru **Zestaw rekordów aliasów**. Następnie wybierz opcję **Zestaw rekordów strefy**.
6. Dla opcji **Zestaw rekordów strefy** wybierz rekord **serwer**.
7. Wybierz przycisk **OK**.

## <a name="test-the-alias-record"></a>Testowanie rekordu aliasu

1. Uruchom ulubione narzędzie nslookup. Jedną z opcji jest przechodzenie do [https://network-tools.com/nslook](https://network-tools.com/nslook) .
2. Ustaw typ zapytania dla rekordów i Wyszukaj **test. \<your domain name\>**. W odpowiedzi otrzymasz **10.10.10.10**.
3. W witrynie Azure Portal zmień rekord A **serwer** na **10.11.11.11**.
4. Poczekaj kilka minut, a następnie użyj ponownie narzędzia nslookup dla rekordu **test**. W odpowiedzi otrzymasz **10.11.11.11**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych w ramach tego samouczka, usuń rekordy zasobów **serwer** i **test** w swojej strefie.


## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono rekord aliasu do odwoływania do rekordu zasobów w strefie DNS. Aby dowiedzieć się więcej o usłudze Azure DNS i aplikacjach internetowych, przejdź do samouczka dotyczącego aplikacji internetowych.

> [!div class="nextstepaction"]
> [Create DNS records for a web app in a custom domain (Tworzenie rekordów DNS aplikacji internetowej w domenie niestandardowej)](./dns-web-sites-custom-domain.md)
