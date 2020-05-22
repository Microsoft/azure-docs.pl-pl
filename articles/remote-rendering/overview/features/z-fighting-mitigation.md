---
title: Ograniczanie efektu Z-fighting
description: Opisuje techniki ograniczające artefakty do walki z
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 69774c0014aac26c7266620bbe7d06ba37d6023b
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758813"
---
# <a name="z-fighting-mitigation"></a>Ograniczanie efektu Z-fighting

Gdy dwie powierzchnie nakładają się, nie jest jasne, które powinny być renderowane na drugim. Wynik jest nawet różny dla pikseli, co powoduje artefakty zależne od widoku. W związku z tym, gdy kamera lub siatka przesunie się, te wzorce migotają zauważalnie. Ten artefakt jest nazywany *z-walką*. W przypadku aplikacji AR i VR problem został wzmocniony, ponieważ urządzenia z zainstalowanym głową są zawsze przenoszone. Aby uniemożliwić podgląd niewygodną funkcję ograniczania ochrony przed walką z usługą Azure, zdalne renderowanie jest dostępne.

## <a name="z-fighting-mitigation-modes"></a>Tryby ograniczania do walki Z

|Istniał                        | Wynik                               |
|---------------------------------|:-------------------------------------|
|Regularna walka z               |![Walka Z](./media/zfighting-0.png)|
|Ograniczenie do walki Z włączonymi ograniczeniami    |![Walka Z](./media/zfighting-1.png)|
|Włączono podświetlanie szachownicy|![Walka Z](./media/zfighting-2.png)|

Poniższy kod włącza środki zaradcze do walki z:

```cs
void EnableZFightingMitigation(AzureSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Actions.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

```cpp
void EnableZFightingMitigation(ApiHandle<AzureSession> session, bool highlight)
{
    ApiHandle<ZFightingMitigationSettings> settings = *session->Actions()->ZFightingMitigationSettings();

    // enabling z-fighting mitigation
    settings->Enabled(true);

    // enabling checkerboard highlighting of z-fighting potential
    settings->Highlighting(highlight);
}
```


> [!NOTE]
> Łagodzenie Z-walk jest ustawieniem globalnym, które ma wpływ na wszystkie renderowane siatki.

## <a name="reasons-for-z-fighting"></a>Przyczyny dotyczące walki z

Walka Z systemem odbywa się głównie z dwóch powodów:

1. gdy powierzchnie są bardzo daleko od aparatu, precyzja ich wartości głębokości obniży się, a wartości stają się niemożliwymi do odróżnienia
1. gdy powierzchnie w sieci fizycznie nakładają się

Pierwszy problem może się zawsze zdarzyć i trudno go wyeliminować. Jeśli tak się dzieje w aplikacji, upewnij się, że stosunek odległości *bliskiej płaszczyzny* do odległości o dużej *płaszczyźnie* jest niski jako praktyczny. Na przykład bliska płaszczyzna na odległość 0,01 i międzypłaszczyzna na odległość 1000 spowoduje utworzenie tego problemu znacznie wcześniej, niż w przypadku bliskiej płaszczyzny na 0,1 i na dalekiej płaszczyźnie na odległość 20.

Drugi problem jest wskaźnikiem dla nieprawidłowo napisanej zawartości. W świecie rzeczywistym dwa obiekty nie mogą znajdować się w tym samym miejscu w tym samym czasie. W zależności od aplikacji użytkownicy mogą chcieć wiedzieć, czy nakładające się powierzchnie są dostępne i gdzie się znajdują. Na przykład scena CAD budynku, która jest podstawą dla budownictwa rzeczywistego, nie powinna zawierać fizycznie niemożliwych przecięć powierzchni. Aby można było przeprowadzić inspekcję wizualną, dostępny jest tryb wyróżniania, który wyświetla potencjalną ocenę z wizualizacji jako animowany wzór szachownicy.

## <a name="limitations"></a>Ograniczenia

Zapewnione środki zaradcze z zakresu walki są najlepszym rozwiązaniem. Nie ma gwarancji, że usuwa wszystkie z walk. Również automatycznie preferuje jedną powierzchnię na innym. W ten sposób, gdy masz powierzchnie, które są zbyt blisko siebie, może się zdarzyć, że powierzchnia "niewłaściwa" zakończy się w górnej części. Typowym przypadkiem problemu jest to, że do powierzchni są stosowane teksty i inne licencje. W przypadku ograniczania środków z ograniczeniami do walki te szczegóły mogą być łatwo znikane.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

* Włączenie ograniczania do walki z niewielkim obciążeniem wiąże się ze zmniejszeniem wydajności.
* Ponadto włączenie nakładki z-walką wiąże się z nieprostym obciążeniem wydajności, ale może się to różnić w zależności od sceny.

## <a name="next-steps"></a>Następne kroki

* [Tryby renderowania](../../concepts/rendering-modes.md)
* [Reprojekcja na późnym etapie](late-stage-reprojection.md)
