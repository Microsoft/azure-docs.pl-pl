---
title: Zarządzanie zestawami rekordów i rekordami DNS za pomocą Azure DNS
description: Azure DNS oferuje możliwość zarządzania zestawami rekordów DNS i rekordami podczas hostowania domeny.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 10/6/2018
ms.author: rohink
ms.openlocfilehash: 111d94db1cbec658daabfb1a4c38c8160d6f50b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84696835"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Zarządzanie rekordami i zestawami rekordów DNS przy użyciu Azure Portal

W tym artykule pokazano, jak zarządzać zestawami rekordów i rekordami dla strefy DNS przy użyciu Azure Portal.

Ważne jest, aby zrozumieć różnicę między zestawami rekordów DNS i pojedynczymi rekordami DNS. Zestaw rekordów jest kolekcją rekordów w strefie o tej samej nazwie i są tego samego typu. Aby uzyskać więcej informacji, zobacz [Tworzenie zestawów rekordów DNS i rekordów przy użyciu Azure Portal](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Utwórz nowy zestaw rekordów i rekord

Aby utworzyć zestaw rekordów w Azure Portal, zobacz [tworzenie rekordów DNS przy użyciu Azure Portal](dns-getstarted-create-recordset-portal.md).

## <a name="view-a-record-set"></a>Wyświetl zestaw rekordów

1. W Azure Portal przejdź do bloku **strefa DNS** .
2. Wyszukaj zestaw rekordów i wybierz go. Spowoduje to otwarcie właściwości zestawu rekordów.

    ![Wyszukaj zestaw rekordów](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Dodawanie nowego rekordu do zestawu rekordów

Można dodać do 20 rekordów do dowolnego zestawu rekordów. Zestaw rekordów nie może zawierać dwóch identycznych rekordów. Można tworzyć puste zestawy rekordów (z rekordami zerowymi), ale nie są one wyświetlane na serwerach nazw Azure DNS. Zestawy rekordów typu CNAME mogą zawierać jeden rekord.

1. W bloku **Właściwości zestawu rekordów** dla strefy DNS kliknij zestaw rekordów, do którego chcesz dodać rekord.

    ![Wybierz zestaw rekordów](./media/dns-operations-recordsets-portal/selectset500.png)

2. Określ właściwości zestawu rekordów, wypełniając pola.

    ![Dodawanie rekordu](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Kliknij przycisk **Zapisz** w górnej części bloku, aby zapisać ustawienia. Następnie zamknij blok.
4. W rogu zobaczysz, że rekord jest zapisywany.

    ![Zapisywanie zestawu rekordów](./media/dns-operations-recordsets-portal/saving150.png)

Po zapisaniu rekordu wartości w bloku **strefy DNS** zostaną odzwierciedlone w nowym rekordzie.

## <a name="update-a-record"></a>Zaktualizowanie rekordu

W przypadku aktualizowania rekordu w istniejącym zestawie rekordów, pola, które można zaktualizować, zależą od typu rekordu, z którym pracujesz.

1. W bloku **Właściwości zestawu rekordów** dla zestawu rekordów Wyszukaj rekord.
2. Zmodyfikuj rekord. Podczas modyfikowania rekordu można zmienić dostępne ustawienia dla rekordu. W poniższym przykładzie pole **adres IP** jest zaznaczone, a adres IP jest w trakcie modyfikowania.

    ![Modyfikowanie rekordu](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Kliknij przycisk **Zapisz** w górnej części bloku, aby zapisać ustawienia. W prawym górnym rogu zobaczysz powiadomienie, że rekord został zapisany.

    ![Zapisano zestaw rekordów](./media/dns-operations-recordsets-portal/saved150.png)

Po zapisaniu rekordu wartości ustawione w bloku **strefy DNS** zostaną odzwierciedlone w zaktualizowanym rekordzie.

## <a name="remove-a-record-from-a-record-set"></a>Usuwanie rekordu z zestawu rekordów

Możesz użyć Azure Portal, aby usunąć rekordy z zestawu rekordów. Należy zauważyć, że usunięcie ostatniego rekordu z zestawu rekordów nie powoduje usunięcia zestawu rekordów.

1. W bloku **Właściwości zestawu rekordów** dla zestawu rekordów Wyszukaj rekord.
2. Kliknij rekord, który chcesz usunąć. Następnie wybierz pozycję **Usuń**.

    ![Usuń rekord](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Kliknij przycisk **Zapisz** w górnej części bloku, aby zapisać ustawienia.
4. Po usunięciu rekordu wartości rekordu w bloku **strefy DNS** będą odzwierciedlać usunięcie.

## <a name="delete-a-record-set"></a><a name="delete"></a>Usuwanie zestawu rekordów

1. W bloku **Właściwości zestawu rekordów** dla zestawu rekordów kliknij przycisk **Usuń**.

    ![Usuwanie zestawu rekordów](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. Zostanie wyświetlony komunikat z pytaniem, czy chcesz usunąć zestaw rekordów.
3. Sprawdź, czy nazwa jest zgodna z zestawem rekordów, który chcesz usunąć, a następnie kliknij przycisk **tak**.
4. W bloku **strefa DNS** Sprawdź, czy zestaw rekordów nie jest już widoczny.

## <a name="work-with-ns-and-soa-records"></a>Współpraca z rekordami NS i SOA

Rekordy NS i SOA tworzone automatycznie są zarządzane inaczej od innych typów rekordów.

### <a name="modify-soa-records"></a>Modyfikuj rekordy SOA

Nie można dodawać ani usuwać rekordów z automatycznie utworzonego rekordu SOA w obszarze wierzchołka strefy (nazwa = " \@ "). Można jednak zmodyfikować dowolny z parametrów w rekordzie SOA (z wyjątkiem "host") i zestawem rekordów TTL.

### <a name="modify-ns-records-at-the-zone-apex"></a>Modyfikowanie rekordów NS w wierzchołku strefy

Rekord NS ustawiony w wierzchołku strefy jest automatycznie tworzony przy użyciu każdej strefy DNS. Zawiera nazwy serwerów nazw Azure DNS przypisanych do strefy.

Do tego zestawu rekordów NS można dodać kolejne serwery nazw, aby obsługiwać domeny współpracujące z więcej niż jednym dostawcą DNS. Możesz również zmodyfikować czas wygaśnięcia i metadane dla tego zestawu rekordów. Nie można jednak usunąć ani zmodyfikować wstępnie wypełnionych serwerów nazw Azure DNS.

Należy zauważyć, że ma to zastosowanie tylko do zestawu rekordów NS w wierzchołku strefy. Inne zestawy rekordów NS w strefie (używane do delegowania stref podrzędnych) można modyfikować bez ograniczenia.

### <a name="delete-soa-or-ns-record-sets"></a>Usuń zestawy rekordów SOA lub NS

Nie można usunąć zestawów rekordów SOA i NS we wierzchołku strefy (nazwa = " \@ "), które są tworzone automatycznie podczas tworzenia strefy. Są one usuwane automatycznie po usunięciu strefy.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat Azure DNS, zobacz [omówienie Azure DNS](dns-overview.md).
* Aby uzyskać więcej informacji na temat automatyzowania systemu DNS, zobacz [Tworzenie stref DNS i zestawów rekordów przy użyciu zestawu .NET SDK](dns-sdk.md).
* Aby uzyskać więcej informacji na temat rekordów odwrotnego systemu DNS, zobacz [Omówienie zwrotnego systemu DNS i pomocy technicznej na platformie Azure](dns-reverse-dns-overview.md).
* Aby uzyskać więcej informacji na temat rekordów aliasów Azure DNS, zobacz [Azure DNS rekordy aliasów — Omówienie](dns-alias.md).
