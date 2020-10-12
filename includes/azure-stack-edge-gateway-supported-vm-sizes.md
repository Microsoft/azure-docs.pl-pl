---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/03/2020
ms.author: alkohli
ms.openlocfilehash: 7ce49873b4e59bcf474deaea4420f56a72c9c589
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89085744"
---
Rozmiar maszyny wirtualnej określa ilość zasobów obliczeniowych, takich jak procesor CPU, procesor GPU i pamięć, które są dostępne dla maszyny wirtualnej. Tworzone maszyny wirtualne powinny mieć rozmiar odpowiadający obciążeniu. Mimo że wszystkie maszyny będą uruchomione na tym samym sprzęcie, rozmiary maszyn mają różne limity dostępu do dysku, co ułatwia zarządzanie ogólnym dostępem do dysku na maszynach wirtualnych. Jeśli obciążenie zwiększy się, można również zmienić rozmiar istniejącej maszyny wirtualnej.

Następujące standardowe maszyny wirtualne serii Dv2 są obsługiwane na potrzeby tworzenia na Azure Stack urządzeniach brzegowych.

### <a name="dv2-series"></a>Seria Dv2
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazyn tymczasowy (GiB)  | Maksymalna przepływność dysku systemu operacyjnego (IOPS) | Maksymalna przepływność magazynu (IOPS) | Maksymalna liczba dysków danych/przepływność (IOPS) | Maksymalna liczba kart sieciowych |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standardowa_D1_v2** |1   |3,5 |50   |500 |3000  |4 / 4 x 500   |2 |
|**Standardowa_D2_v2** |2   |7   |100  |500 |6000  |8 / 8 x 500   |2 |
|**Standardowa_D3_v2** |4   |14  |200  |500 |12000 |16 / 16 x 500 |4 |
|**Standardowa_D4_v2** |8   |28  |400  |500 |24000 |32 / 32 x 500 |8 |
|**Standardowa_D5_v2** |16  |56  |800  |500 |48000 |64/64x500 |8 |

### <a name="dsv2-series"></a>Seria DSv2
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazyn tymczasowy (GiB)  | Maksymalna przepływność dysku systemu operacyjnego (IOPS) | Maksymalna przepływność magazynu (IOPS) | Maksymalna liczba dysków danych/przepływność (IOPS) | Maksymalna liczba kart sieciowych |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standardowa_DS1_v2** |1   |3,5 |7   |1000 |4000  |4/4x2300   |2 |
|**Standard_DS2_v2** |2   |7   |14  |1000 |8000  |8/8x2300   |2 |
|**Standardowa_DS3_v2** |4   |14  |28  |1000 |16000 |16/16x2300 |4 |
|**Standardowa_DS4_v2** |8   |28  |56  |1000 |32000 |32/32x2300 |8 |
|**Standard_DS5_v2** |16  |56  |112 |1000 |64000 |64/64x2300 |8 |

### <a name="dv2-series"></a>Seria Dv2
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazyn tymczasowy (GiB)  | Maksymalna przepływność dysku systemu operacyjnego (IOPS) | Maksymalna przepływność magazynu (IOPS) | Maksymalna liczba dysków danych/przepływność (IOPS) | Maksymalna liczba kart sieciowych |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standardowa_D11_v2** |2   |14  |100  |500 |6000   |8 / 8 x 500    |2 |
|**Standardowa_D12_v2** |4   |28  |200  |500 |12000  |16 / 16 x 500  |4 |
|**Standardowa_D13_v2** |8   |56  |400  |500 |24000  |32 / 32 x 500  |8 |
|**Standardowa_D14_v2** |16  |112 |800  |500 |48000  |64/64x500  |8 |


### <a name="dsv2-series"></a>Seria DSv2
|Rozmiar     |Procesor wirtualny     |Pamięć (GiB) | Magazyn tymczasowy (GiB)  | Maksymalna przepływność dysku systemu operacyjnego (IOPS) | Maksymalna przepływność magazynu (IOPS) | Maksymalna liczba dysków danych/przepływność (IOPS) | Maksymalna liczba kart sieciowych |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standardowa_DS11_v2** |2   |14  |28   |1000 |8000   |4/4x2300    |2 |
|**Standardowa_DS12_v2** |4   |28  |56   |1000 |16000  |8/8x2300    |4 |
|**Standardowa_DS13_v2** |8   |56  |112  |1000 |32000  |16/16x2300  |8 |
|**Standard_DS14_v2** |16  |112 |224  |1000 |64000  |32/32x2300  |8 |

Aby uzyskać więcej informacji, przejdź do [serii Dv2 na ogólnego przeznaczenia rozmiary maszyn wirtualnych](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series).
