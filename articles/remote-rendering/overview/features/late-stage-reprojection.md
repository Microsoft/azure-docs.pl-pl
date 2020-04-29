---
title: Reprojekcja na późnym etapie
description: Informacje o późnym przemieszczeniu projektu i sposobach ich użycia.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 4aa1148e544ff3451aa1cb956bc4a5fb932b9611
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680988"
---
# <a name="late-stage-reprojection"></a>Reprojekcja na późnym etapie

Przechodzenie do *późnych etapów* (LSR) to funkcja sprzętowa, która ułatwia ustabilizowanie hologramów podczas przenoszenia użytkownika.

Modele statyczne powinny wizualnie zachować swoją pozycję podczas poruszania się po nich. Jeśli wydaje się to niestabilne, takie zachowanie może być przyczyną problemów z LSR. Należy pamiętać, że dodatkowe przekształcenia dynamiczne, takie jak animacje lub widoki rozłożenia, mogą maskować takie zachowanie.

Można wybrać jeden z dwóch różnych trybów LSR, mianowicie **planarny LSR** lub **głębokość LSR**. Który jest aktywny, zależy od tego, czy aplikacja kliencka przesyła bufor głębokości.

Oba tryby LSR zwiększają stabilność hologramu, chociaż mają odrębne ograniczenia. Zacznij od uzyskania większej głębi LSR, ponieważ raczej w większości przypadków lepsze wyniki.

## <a name="choose-lsr-mode-in-unity"></a>Wybieranie trybu LSR w środowisku Unity

W edytorze aparatu Unity przejdź do *pliku > ustawienia kompilacji*. Wybierz pozycję *Ustawienia odtwarzacza* w lewym dolnym rogu, a następnie sprawdź w obszarze *Player > ustawienia XR > wirtualne zestawy SDK > Windows Mixed Reality* , czy jest zaznaczone pole wyboru **Włącz udostępnianie buforu głębokości** :

![Flaga włączenia udostępniania buforu głębokości](./media/unity-depth-buffer-sharing-enabled.png)

Jeśli tak, aplikacja będzie używać głębokości LSR, w przeciwnym razie będzie używać planarnych LSR.

## <a name="depth-lsr"></a>Głębokość LSR

Aby głębokość LSR działała, aplikacja kliencka musi dostarczyć prawidłowy bufor głębokości, który zawiera wszystkie istotne dane geometryczne, które należy wziąć pod uwagę podczas LSR.

Głębokość LSR próbuje stabilizację ramki wideo na podstawie zawartości dostarczonego buforu głębokości. W rezultacie zawartość, która nie została renderowana, taka jak obiekty przezroczyste, nie może być dostosowywana przez LSR i może zawierać artefakty niestabilności i reprojektowania.

## <a name="planar-lsr"></a>LSR planarny

Współrzędne LSR nie zawierają informacji o głębokości na piksel, ponieważ głębokości LSR. Zamiast tego reprojektuje całą zawartość na podstawie płaszczyzny, w której należy udostępnić każdą klatkę.

Planarny LSR reprojektuje obiekty najlepiej, które znajdują się blisko podanej płaszczyzny. Jeszcze bardziej niestabilny obiekt będzie wyglądał. Mimo że głębia LSR jest lepsza podczas reprojektowania obiektów na różnych głębokościach, współrzędne LSR mogą być lepsze do dopasowania zawartości do płaszczyzny.

### <a name="configure-planar-lsr-in-unity"></a>Konfigurowanie planarnych LSR w środowisku Unity

Parametry płaszczyzny są wyprowadzane z tak zwanego *punktu fokusu*, który należy dostarczyć każdą klatkę `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame`. Aby uzyskać szczegółowe informacje, zobacz [interfejs API punktu fokusu aparatu Unity](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) . Jeśli nie ustawisz punktu fokusu, zostanie wybrana opcja powrotu. Jednak automatyczna rezerwa często prowadzi do nieoptymalnych wyników.

Punkt fokusu można obliczyć samodzielnie, ale może się to okazać podstawą dla tego, który jest obliczany przez hosta renderowania zdalnego. Wywołanie `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` w celu uzyskania tego. Zostanie wyświetlony monit o podanie ramki współrzędnej, w której ma zostać wyświetlona punkt fokusu. W większości przypadków należy jedynie podać wynik z `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` tego miejsca.

Zazwyczaj zarówno klient, jak i Host renderują zawartość, której nie zna druga strona, na przykład elementy interfejsu użytkownika na komputerze klienckim. W związku z tym warto połączyć zdalny punkt skupienia z lokalnym obliczanym elementem.

Punkty fokusu obliczone w dwóch kolejnych klatkach mogą być zupełnie inne. Po prostu użycie ich jako-jest możliwe do przeskoczenia do hologramów. Aby uniknąć tego zachowania, zaleca się interpolowanie między poprzednim i bieżącym punktem fokusu.

## <a name="next-steps"></a>Następne kroki

* [Zapytania wydajności po stronie serwera](performance-queries.md)
