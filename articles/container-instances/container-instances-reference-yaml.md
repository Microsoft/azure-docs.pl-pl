---
title: YAML odwołanie do grupy kontenerów
description: Odwołanie do pliku YAML obsługiwanego przez Azure Container Instances w celu skonfigurowania grupy kontenerów
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: d0ec8d13eebba1c60f5a52f8c43bdd8b90eeb913
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87084764"
---
# <a name="yaml-reference-azure-container-instances"></a>Odwołanie YAML: Azure Container Instances

W tym artykule opisano składnię i właściwości pliku YAML obsługiwanego przez Azure Container Instances w celu skonfigurowania [grupy kontenerów](container-instances-container-groups.md). Użyj pliku YAML, aby wprowadzić konfigurację grupy do polecenia [AZ Container Create][az-container-create] w interfejsie CLI platformy Azure. 

Plik YAML jest wygodnym sposobem konfigurowania grupy kontenerów dla powtarzalnych wdrożeń. Jest to zwięzła alternatywa dla używania [szablonu Menedżer zasobów](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups) lub zestawów SDK Azure Container Instances do tworzenia lub aktualizowania grupy kontenerów.

> [!NOTE]
> To odwołanie dotyczy plików YAML w wersji interfejsu API REST Azure Container Instances `2019-12-01` .

## <a name="schema"></a>Schemat 

Schemat pliku YAML jest następujący, w tym komentarze do wyróżniania właściwości klucza. Aby uzyskać opis właściwości w tym schemacie, zapoznaj się z sekcją [wartości właściwości](#property-values) .

```yml
name: string  # Name of the container group
apiVersion: '2019-12-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
  sku: string # SKU for the container group
  encryptionProperties:
    vaultBaseUrl: string
    keyName: string
    keyVersion: string
  initContainers: # Array of init containers in the group
  - name: string
    properties:
      image: string
      command:
      - string
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      volumeMounts:
      - name: string
        mountPath: string
        readOnly: boolean
```

## <a name="property-values"></a>Wartości właściwości

W poniższych tabelach opisano wartości, które należy ustawić w schemacie.



### <a name="microsoftcontainerinstancecontainergroups-object"></a>Obiekt Microsoft. ContainerInstance/containerGroups

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Tak | Nazwa grupy kontenerów. |
|  apiVersion | enum | Tak | 2018-10-01 |
|  location | ciąg | Nie | Lokalizacja zasobu. |
|  tags | object | Nie | Tagi zasobów. |
|  identity | object | Nie | Tożsamość grupy kontenerów, jeśli została skonfigurowana. - [Obiekt ContainerGroupIdentity](#containergroupidentity-object) |
|  properties | object | Tak | [Obiekt ContainerGroupProperties](#containergroupproperties-object) |




### <a name="containergroupidentity-object"></a>Obiekt ContainerGroupIdentity

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  typ | enum | Nie | Typ tożsamości używany dla grupy kontenerów. Typ "SystemAssigned, UserAssigned" obejmuje zarówno niejawnie utworzoną tożsamość, jak i zestaw tożsamości przypisanych do użytkownika. Typ "none" usunie wszystkie tożsamości z grupy kontenerów. -SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, brak |
|  Resourceidentity | object | Nie | Lista tożsamości użytkowników skojarzonych z grupą kontenerów. Odwołania do klucza słownika tożsamości użytkownika będą Azure Resource Manager identyfikatorów zasobów w postaci: "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}". |




### <a name="containergroupproperties-object"></a>Obiekt ContainerGroupProperties

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  containers | array | Tak | Kontenery w obrębie grupy kontenerów. - [Obiekt kontenera](#container-object) |
|  imageRegistryCredentials | array | Nie | Poświadczenia rejestru obrazu, na podstawie których zostanie utworzona grupa kontenerów. - [Obiekt ImageRegistryCredential](#imageregistrycredential-object) |
|  restartPolicy | enum | Nie | Zasady ponownego uruchamiania dla wszystkich kontenerów w grupie kontenerów. - `Always` Zawsze uruchamiaj ponownie `OnFailure` po niepowodzeniu — `Never` nigdy nie uruchamiaj ponownie. -Zawsze, OnFailure, nigdy |
|  Adresu | object | Nie | Typ adresu IP grupy kontenerów. - [IpAddress — obiekt](#ipaddress-object) |
|  osType | enum | Tak | Typ systemu operacyjnego wymagany przez kontenery w grupie kontenerów. — Windows lub Linux |
|  volumes | array | Nie | Lista woluminów, które mogą być instalowane przez kontenery w tej grupie kontenerów. - [Obiekt woluminu](#volume-object) |
|  Diagnostyka | object | Nie | Informacje diagnostyczne dla grupy kontenerów. - [Obiekt ContainerGroupDiagnostics](#containergroupdiagnostics-object) |
|  networkProfile | object | Nie | Informacje o profilu sieciowym dla grupy kontenerów. - [Obiekt ContainerGroupNetworkProfile](#containergroupnetworkprofile-object) |
|  Powodzenie | object | Nie | Informacje o konfiguracji DNS dla grupy kontenerów. - [Obiekt DnsConfiguration](#dnsconfiguration-object) |
| sku | enum | Nie | Jednostka SKU grupy kontenerów — standardowa lub dedykowana |
| encryptionProperties | object | Nie | Właściwości szyfrowania dla grupy kontenerów. - [Obiekt EncryptionProperties](#encryptionproperties-object) | 
| initContainers | array | Nie | Kontenery init dla grupy kontenerów. - [Obiekt InitContainerDefinition](#initcontainerdefinition-object) |




### <a name="container-object"></a>Obiekt kontenera

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Tak | Nazwa podana przez użytkownika wystąpienia kontenera. |
|  properties | object | Tak | Właściwości wystąpienia kontenera. - [Obiekt ContainerProperties](#containerproperties-object) |




### <a name="imageregistrycredential-object"></a>Obiekt ImageRegistryCredential

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  serwer | ciąg | Tak | Serwer rejestru platformy Docker bez protokołu, takiego jak "http" i "https". |
|  nazwa użytkownika | ciąg | Tak | Nazwa użytkownika dla rejestru prywatnego. |
|  hasło | ciąg | Nie | Hasło do rejestru prywatnego. |




### <a name="ipaddress-object"></a>IpAddress — obiekt

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  ports | array | Tak | Lista portów uwidocznionych w grupie kontenerów. - [Obiekt portu](#port-object) |
|  typ | enum | Tak | Określa, czy adres IP jest narażony na publiczny Internet lub prywatną sieć wirtualną. — Publiczne lub prywatne |
|  IP | ciąg | Nie | Adres IP uwidoczniony dla publicznego Internetu. |
|  dnsNameLabel | ciąg | Nie | Etykieta nazwy DNS dla adresu IP. |




### <a name="volume-object"></a>Obiekt woluminu

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Tak | Nazwa woluminu. |
|  azureFile | object | Nie | Wolumin plików platformy Azure. - [Obiekt AzureFileVolume](#azurefilevolume-object) |
|  emptyDir | object | Nie | Pusty wolumin katalogu. |
|  wpis tajny | object | Nie | Wolumin tajny. |
|  gitRepo | object | Nie | Wolumin repozytorium git. - [Obiekt GitRepoVolume](#gitrepovolume-object) |




### <a name="containergroupdiagnostics-object"></a>Obiekt ContainerGroupDiagnostics

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | Nie | Informacje o usłudze log Analytics grupy kontenerów. - [Obiekt LogAnalytics](#loganalytics-object) |




### <a name="containergroupnetworkprofile-object"></a>Obiekt ContainerGroupNetworkProfile

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  identyfikator | ciąg | Tak | Identyfikator profilu sieciowego. |




### <a name="dnsconfiguration-object"></a>Obiekt DnsConfiguration

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  Serwery nazw | array | Tak | Serwery DNS dla grupy kontenerów. -String |
|  searchDomains | ciąg | Nie | Domeny wyszukiwania DNS dla wyszukiwania nazw hostów w grupie kontenerów. |
|  opcje | ciąg | Nie | Opcje usługi DNS dla grupy kontenerów. |


### <a name="encryptionproperties-object"></a>Obiekt EncryptionProperties

| Nazwa  | Typ  | Wymagane  | Wartość |
|  ---- | ---- | ---- | ---- |
| vaultBaseUrl  | ciąg    | Tak   | Podstawowy adres URL magazynu kluczy. |
| keyName   | ciąg    | Tak   | Nazwa klucza szyfrowania. |
| Wersja programu    | ciąg    | Tak   | Wersja klucza szyfrowania. |

### <a name="initcontainerdefinition-object"></a>Obiekt InitContainerDefinition

| Nazwa  | Typ  | Wymagane  | Wartość |
|  ---- | ---- | ---- | ---- |
| name  | ciąg |  Tak | Nazwa kontenera init. |
| properties    | object    | Tak   | Właściwości kontenera init. - [Obiekt InitContainerPropertiesDefinition](#initcontainerpropertiesdefinition-object)


### <a name="containerproperties-object"></a>Obiekt ContainerProperties

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  image (obraz) | ciąg | Tak | Nazwa obrazu użytego do utworzenia wystąpienia kontenera. |
|  command | array | Nie | Polecenia do wykonania w ramach wystąpienia kontenera w formularzu exec. -String |
|  ports | array | Nie | Uwidocznione porty w wystąpieniu kontenera. - [Obiekt ContainerPort](#containerport-object) |
|  environmentVariables | array | Nie | Zmienne środowiskowe do ustawienia w wystąpieniu kontenera. - [Obiekt zmiennych środowiskowych](#environmentvariable-object) |
|  zasoby | object | Tak | Wymagania dotyczące zasobów wystąpienia kontenera. - [Obiekt ResourceRequirements](#resourcerequirements-object) |
|  volumeMounts | array | Nie | Zainstaluj woluminy dostępne dla wystąpienia kontenera. - [Obiekt VolumeMount](#volumemount-object) |
|  livenessProbe | object | Nie | Sonda na żywo. - [Obiekt ContainerProbe](#containerprobe-object) |
|  readinessProbe | object | Nie | Sonda gotowości. - [Obiekt ContainerProbe](#containerprobe-object) |




### <a name="port-object"></a>Obiekt portu

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  protokol | enum | Nie | Protokół skojarzony z portem. -TCP lub UDP |
|  port | liczba całkowita | Tak | Numer portu. |




### <a name="azurefilevolume-object"></a>Obiekt AzureFileVolume

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  shareName | ciąg | Tak | Nazwa udziału plików platformy Azure, który ma zostać zainstalowany jako wolumin. |
|  Trybie | boolean | Nie | Flaga wskazująca, czy plik platformy Azure udostępniony jako wolumin jest tylko do odczytu. |
|  storageAccountName | ciąg | Tak | Nazwa konta magazynu zawierającego udział plików platformy Azure. |
|  storageAccountKey | ciąg | Nie | Klucz dostępu konta magazynu używany do uzyskiwania dostępu do udziału plików platformy Azure. |




### <a name="gitrepovolume-object"></a>Obiekt GitRepoVolume

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  katalog | ciąg | Nie | Nazwa katalogu docelowego. Nie może zawierać ani zaczynać się od "..".  Jeśli zostanie podana wartość ".", katalog woluminu będzie repozytorium git.  W przeciwnym razie wolumin będzie zawierać repozytorium Git w podkatalogu o podanej nazwie. |
|  repozytorium | ciąg | Tak | Adres URL repozytorium |
|  revision | ciąg | Nie | Skrót zatwierdzenia dla określonej poprawki. |



### <a name="loganalytics-object"></a>Obiekt LogAnalytics

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  workspaceId | ciąg | Tak | Identyfikator obszaru roboczego dla usługi log Analytics |
|  workspaceKey | ciąg | Tak | Klucz obszaru roboczego dla usługi log Analytics |
|  logType | enum | Nie | Typ dziennika, który ma być używany. -ContainerInsights lub ContainerInstanceLogs |
|  metadane | object | Nie | Metadane usługi log Analytics. |


### <a name="initcontainerpropertiesdefinition-object"></a>Obiekt InitContainerPropertiesDefinition

| Nazwa  | Typ  | Wymagane  | Wartość |
|  ---- | ---- | ---- | ---- |
| image (obraz) | ciąg    | Nie    | Obraz kontenera init. |
| command   | array | Nie    | Polecenie do wykonania w kontenerze init w formularzu exec. -String |
| environmentVariables | array  | Nie |Zmienne środowiskowe do ustawienia w kontenerze init. - [Obiekt zmiennych środowiskowych](#environmentvariable-object)
| volumeMounts |array   | Nie    | Instalacje woluminu są dostępne dla kontenera init. - [Obiekt VolumeMount](#volumemount-object)

### <a name="containerport-object"></a>Obiekt ContainerPort

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  protokol | enum | Nie | Protokół skojarzony z portem. -TCP lub UDP |
|  port | liczba całkowita | Tak | Numer portu uwidoczniony w obrębie grupy kontenerów. |




### <a name="environmentvariable-object"></a>Obiekt zmiennych środowiskowych

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Tak | Nazwa zmiennej środowiskowej. |
|  wartość | ciąg | Nie | Wartość zmiennej środowiskowej. |
|  secureValue | ciąg | Nie | Wartość zmiennej środowiskowej Secure. |




### <a name="resourcerequirements-object"></a>Obiekt ResourceRequirements

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  żądań | object | Tak | Żądania zasobów tego wystąpienia kontenera. - [Obiekt ResourceRequests](#resourcerequests-object) |
|  ograniczeń | object | Nie | Limity zasobów tego wystąpienia kontenera. - [Obiekt ResourceLimits](#resourcelimits-object) |




### <a name="volumemount-object"></a>Obiekt VolumeMount

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Tak | Nazwa instalacji woluminu. |
|  mountPath | ciąg | Tak | Ścieżka wewnątrz kontenera, w którym należy zainstalować wolumin. Nie może zawierać dwukropka (:). |
|  Trybie | boolean | Nie | Flaga wskazująca, czy instalacja woluminu jest tylko do odczytu. |




### <a name="containerprobe-object"></a>Obiekt ContainerProbe

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  szef | object | Nie | Wykonywanie polecenia do sondowania — [obiekt ContainerExec](#containerexec-object) |
|  Narzędzia HttpGet | object | Nie | Ustawienia http get do Probe- [ContainerHttpGet Object](#containerhttpget-object) |
|  initialDelaySeconds | liczba całkowita | Nie | Początkowe opóźnienie w sekundach. |
|  periodSeconds | liczba całkowita | Nie | Okres w sekundach. |
|  failureThreshold | liczba całkowita | Nie | Próg błędu. |
|  successThreshold | liczba całkowita | Nie | Próg sukcesu. |
|  timeoutSeconds | liczba całkowita | Nie | Limit czasu w sekundach. |




### <a name="resourcerequests-object"></a>Obiekt ResourceRequests

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | liczba | Tak | Żądanie pamięci w GB tego wystąpienia kontenera. |
|  testy | liczba | Tak | Żądanie procesora CPU tego wystąpienia kontenera. |
|  graficzny | object | Nie | Żądanie procesora GPU tego wystąpienia kontenera. - [Obiekt GpuResource](#gpuresource-object) |




### <a name="resourcelimits-object"></a>Obiekt ResourceLimits

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | liczba | Nie | Limit pamięci w GB tego wystąpienia kontenera. |
|  testy | liczba | Nie | Limit procesorów dla tego wystąpienia kontenera. |
|  graficzny | object | Nie | Limit procesora GPU dla tego wystąpienia kontenera. - [Obiekt GpuResource](#gpuresource-object) |




### <a name="containerexec-object"></a>Obiekt ContainerExec

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  command | array | Nie | Polecenia do wykonania w kontenerze. -String |




### <a name="containerhttpget-object"></a>Obiekt ContainerHttpGet

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  path | ciąg | Nie | Ścieżka do sondy. |
|  port | liczba całkowita | Tak | Numer portu do sondowania. |
|  schemat | enum | Nie | Schemat. -http lub https |




### <a name="gpuresource-object"></a>Obiekt GpuResource

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  count | liczba całkowita | Tak | Liczba zasobów procesora GPU. |
|  sku | enum | Tak | Jednostka SKU zasobu GPU. -K80, P100, V100 |


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z samouczkiem [wdrażanie grupy wielokontenerowej przy użyciu pliku YAML](container-instances-multi-container-yaml.md).

Zobacz przykłady użycia pliku YAML do wdrażania grup kontenerów w [sieci wirtualnej](container-instances-vnet.md) lub [instalowania woluminu zewnętrznego](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

