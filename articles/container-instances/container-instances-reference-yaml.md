---
title: Odwołanie YAML dla grupy kontenerów
description: Odwołanie do pliku YAML obsługiwanego Azure Container Instances konfigurowania grupy kontenerów
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: efbea7b64ccdf685d4c82bd406f2aa09227e53e1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771141"
---
# <a name="yaml-reference-azure-container-instances"></a>Informacje o yaml: Azure Container Instances

W tym artykule omykamy składnię i właściwości pliku YAML obsługiwanego Azure Container Instances konfigurowania [grupy kontenerów.](container-instances-container-groups.md) Użyj pliku YAML, aby wprowadzić konfigurację grupy w poleceniu [az container create][az-container-create] w interfejsie wiersza polecenia platformy Azure. 

Plik YAML to wygodny sposób konfigurowania grupy kontenerów dla powtarzalnych wdrożeń. Jest to zwięzła [](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups) alternatywa dla używania szablonu Resource Manager lub Azure Container Instances SDK do tworzenia lub aktualizowania grupy kontenerów.

> [!NOTE]
> Ta dokumentacja dotyczy plików YAML dla Azure Container Instances interfejsu API `2019-12-01` REST.

## <a name="schema"></a>Schemat 

Schemat pliku YAML jest następujący, w tym komentarze do wyróżniania kluczowych właściwości. Opis właściwości w tym schemacie znajduje się w [sekcji Wartości](#property-values) właściwości.

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



### <a name="microsoftcontainerinstancecontainergroups-object"></a>Obiekt Microsoft.ContainerInstance/containerGroups

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Tak | Nazwa grupy kontenerów. |
|  apiVersion | enum | Tak | 2018-10-01 |
|  location | ciąg | Nie | Lokalizacja zasobu. |
|  tags | object | Nie | Tagi zasobów. |
|  identity | object | Nie | Tożsamość grupy kontenerów, jeśli jest skonfigurowana. - [ContainerGroupIdentity, obiekt](#containergroupidentity-object) |
|  properties | object | Tak | [ContainerGroupProperties, obiekt](#containergroupproperties-object) |




### <a name="containergroupidentity-object"></a>ContainerGroupIdentity, obiekt

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  typ | enum | Nie | Typ tożsamości używany dla grupy kontenerów. Typ "SystemAssigned, UserAssigned" obejmuje zarówno niejawnie utworzoną tożsamość, jak i zestaw tożsamości przypisanych przez użytkownika. Typ "Brak" spowoduje usunięcie wszystkich tożsamości z grupy kontenerów. - SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, None |
|  userAssignedIdentities | object | Nie | Lista tożsamości użytkowników skojarzonych z grupą kontenerów. Odwołania do klucza słownika tożsamości użytkownika będą Azure Resource Manager identyfikatorami zasobów w postaci: "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}". |




### <a name="containergroupproperties-object"></a>ContainerGroupProperties, obiekt

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  containers | array | Tak | Kontenery w grupie kontenerów. - [Obiekt kontenera](#container-object) |
|  imageRegistryCredentials | array | Nie | Poświadczenia rejestru obrazów, za pomocą którego jest tworzona grupa kontenerów. - [Obiekt ImageRegistryCredential](#imageregistrycredential-object) |
|  restartPolicy | enum | Nie | Zasady ponownego uruchamiania dla wszystkich kontenerów w grupie kontenerów. - `Always` Zawsze uruchamiaj ponownie `OnFailure` — uruchom ponownie po awarii — nigdy nie `Never` uruchamiaj ponownie. - Always, OnFailure, Never |
|  Ipaddress | object | Nie | Typ adresu IP grupy kontenerów. - [IpAddress, obiekt](#ipaddress-object) |
|  osType | enum | Tak | Typ systemu operacyjnego wymagany przez kontenery w grupie kontenerów. - Windows lub Linux |
|  volumes | array | Nie | Lista woluminów, które mogą być instalowane przez kontenery w tej grupie kontenerów. - [Obiekt woluminu](#volume-object) |
|  Diagnostyka | object | Nie | Informacje diagnostyczne dotyczące grupy kontenerów. - [ContainerGroupDiagnostics, obiekt](#containergroupdiagnostics-object) |
|  networkProfile | object | Nie | Informacje o profilu sieciowym dla grupy kontenerów. - [ContainerGroupNetworkProfile, obiekt](#containergroupnetworkprofile-object) |
|  dnsConfig | object | Nie | Informacje o konfiguracji DNS dla grupy kontenerów. - [Obiekt DnsConfiguration](#dnsconfiguration-object) |
| sku | enum | Nie | SKU dla grupy kontenerów — Standardowa lub Dedykowana |
| encryptionProperties | object | Nie | Właściwości szyfrowania dla grupy kontenerów. - [EncryptionProperties, obiekt](#encryptionproperties-object) | 
| initContainers | array | Nie | Kontenery init dla grupy kontenerów. - [InitContainerDefinition, obiekt](#initcontainerdefinition-object) |




### <a name="container-object"></a>Obiekt kontenera

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Tak | Podano nazwę wystąpienia kontenera podaną przez użytkownika. |
|  properties | object | Tak | Właściwości wystąpienia kontenera. - [ContainerProperties, obiekt](#containerproperties-object) |




### <a name="imageregistrycredential-object"></a>Obiekt ImageRegistryCredential

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  serwer | ciąg | Tak | Serwer rejestru obrazów platformy Docker bez protokołu, takiego jak "http" i "https". |
|  nazwa użytkownika | ciąg | Tak | Nazwa użytkownika rejestru prywatnego. |
|  hasło | ciąg | Nie | Hasło rejestru prywatnego. |




### <a name="ipaddress-object"></a>IpAddress, obiekt

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  ports | array | Tak | Lista portów ujawnionych w grupie kontenerów. - [Obiekt portu](#port-object) |
|  typ | enum | Tak | Określa, czy adres IP jest ujawniony w publicznej lub prywatnej sieci wirtualnej. — Publiczne lub prywatne |
|  IP | ciąg | Nie | Adres IP ujawniony w publicznym Internecie. |
|  dnsNameLabel | ciąg | Nie | Etykieta nazwy DNS dla adresu IP. |




### <a name="volume-object"></a>Obiekt woluminu

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Tak | Nazwa woluminu. |
|  azureFile | object | Nie | Wolumin plików platformy Azure. - [AzureFileVolume, obiekt](#azurefilevolume-object) |
|  emptyDir | object | Nie | Pusty wolumin katalogu. |
|  wpis tajny | object | Nie | Wolumin tajny. |
|  gitRepo | object | Nie | Wolumin repozytorium git. - [GitRepoVolume, obiekt](#gitrepovolume-object) |




### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics, obiekt

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | Nie | Informacje analityczne dzienników grup kontenerów. - [LogAnalytics, obiekt](#loganalytics-object) |




### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile, obiekt

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  identyfikator | ciąg | Tak | Identyfikator profilu sieciowego. |




### <a name="dnsconfiguration-object"></a>Obiekt DnsConfiguration

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  Nameservers | array | Tak | Serwery DNS dla grupy kontenerów. - ciąg |
|  searchDomains | ciąg | Nie | Domeny wyszukiwania DNS do wyszukiwania nazwy hosta w grupie kontenerów. |
|  opcje | ciąg | Nie | Opcje DNS dla grupy kontenerów. |


### <a name="encryptionproperties-object"></a>EncryptionProperties, obiekt

| Nazwa  | Typ  | Wymagane  | Wartość |
|  ---- | ---- | ---- | ---- |
| vaultBaseUrl  | ciąg    | Tak   | Podstawowy adres URL usługi keyvault. |
| keyName   | ciąg    | Tak   | Nazwa klucza szyfrowania. |
| KeyVersion    | ciąg    | Tak   | Wersja klucza szyfrowania. |

### <a name="initcontainerdefinition-object"></a>InitContainerDefinition, obiekt

| Nazwa  | Typ  | Wymagane  | Wartość |
|  ---- | ---- | ---- | ---- |
| name  | ciąg |  Tak | Nazwa kontenera init. |
| properties    | object    | Tak   | Właściwości kontenera init. - [InitContainerPropertiesDefinition, obiekt](#initcontainerpropertiesdefinition-object)


### <a name="containerproperties-object"></a>ContainerProperties, obiekt

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  image (obraz) | ciąg | Tak | Nazwa obrazu użytego do utworzenia wystąpienia kontenera. |
|  command | array | Nie | Polecenia do wykonania w ramach wystąpienia kontenera w postaci exec. - ciąg |
|  ports | array | Nie | Widoczne porty w wystąpieniu kontenera. - [ContainerPort, obiekt](#containerport-object) |
|  environmentVariables | array | Nie | Zmienne środowiskowe do ustawienia w wystąpieniu kontenera. - [EnvironmentVariable, obiekt](#environmentvariable-object) |
|  zasoby | object | Tak | Wymagania dotyczące zasobów wystąpienia kontenera. - [ResourceRequirements, obiekt](#resourcerequirements-object) |
|  volumeMounts | array | Nie | Wolumin jest dostępny dla wystąpienia kontenera. - [VolumeMount, obiekt](#volumemount-object) |
|  livenessProbe | object | Nie | Sonda żywo. - [ContainerProbe, obiekt](#containerprobe-object) |
|  gotowośćProbe | object | Nie | Sonda gotowości. - [ContainerProbe, obiekt](#containerprobe-object) |




### <a name="port-object"></a>Obiekt portu

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  Protokół | enum | Nie | Protokół skojarzony z portem. - TCP lub UDP |
|  port | liczba całkowita | Tak | Numer portu. |




### <a name="azurefilevolume-object"></a>AzureFileVolume, obiekt

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  Nazwaudziału | ciąg | Tak | Nazwa udziału plików platformy Azure, który ma zostać zainstalowany jako wolumin. |
|  Readonly | boolean | Nie | Flaga wskazująca, czy udostępniony plik platformy Azure jest zainstalowany jako wolumin, jest tylko do odczytu. |
|  storageAccountName | ciąg | Tak | Nazwa konta magazynu zawierającego udział plików platformy Azure. |
|  klucz konta magazynu | ciąg | Nie | Klucz dostępu konta magazynu używany do uzyskiwania dostępu do udziału plików platformy Azure. |




### <a name="gitrepovolume-object"></a>GitRepoVolume, obiekt

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  katalog | ciąg | Nie | Nazwa katalogu docelowego. Nie może zawierać ani zaczynać się od "..".  Jeśli zostanie podany ".", katalog woluminu będzie repozytorium git.  W przeciwnym razie, jeśli zostanie określony, wolumin będzie zawierać repozytorium git w podkatalogu o podanej nazwie. |
|  repozytorium | ciąg | Tak | Adres URL repozytorium |
|  revision | ciąg | Nie | Zatwierdzenie skrótu dla określonej poprawki. |



### <a name="loganalytics-object"></a>LogAnalytics, obiekt

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  workspaceId | ciąg | Tak | Identyfikator obszaru roboczego dla usługi Log Analytics |
|  klucz obszaru roboczego | ciąg | Tak | Klucz obszaru roboczego do analizy dzienników |
|  logType | enum | Nie | Typ dziennika, który ma być używany. - ContainerInsights lub ContainerInstanceLogs |
|  metadane | object | Nie | Metadane do analizy dzienników. |


### <a name="initcontainerpropertiesdefinition-object"></a>InitContainerPropertiesDefinition, obiekt

| Nazwa  | Typ  | Wymagane  | Wartość |
|  ---- | ---- | ---- | ---- |
| image (obraz) | ciąg    | Nie    | Obraz kontenera init. |
| command   | array | Nie    | Polecenie do wykonania w kontenerze init w postaci exec. - ciąg |
| environmentVariables | array  | Nie |Zmienne środowiskowe do ustawienia w kontenerze init. - [EnvironmentVariable, obiekt](#environmentvariable-object)
| volumeMounts |array   | Nie    | Wolumin jest dostępny dla kontenera init. - [VolumeMount, obiekt](#volumemount-object)

### <a name="containerport-object"></a>ContainerPort, obiekt

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  Protokół | enum | Nie | Protokół skojarzony z portem. - TCP lub UDP |
|  port | liczba całkowita | Tak | Numer portu ujawniony w grupie kontenerów. |




### <a name="environmentvariable-object"></a>EnvironmentVariable, obiekt

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Tak | Nazwa zmiennej środowiskowej. |
|  wartość | ciąg | Nie | Wartość zmiennej środowiskowej. |
|  secureValue | ciąg | Nie | Wartość bezpiecznej zmiennej środowiskowej. |




### <a name="resourcerequirements-object"></a>ResourceRequirements, obiekt

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  Żądania | object | Tak | Żądania zasobów tego wystąpienia kontenera. - [ResourceRequests, obiekt](#resourcerequests-object) |
|  Limity | object | Nie | Limity zasobów tego wystąpienia kontenera. - [ResourceLimits, obiekt](#resourcelimits-object) |




### <a name="volumemount-object"></a>VolumeMount, obiekt

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  name | ciąg | Tak | Nazwa instalacji woluminu. |
|  ścieżka instalacji | ciąg | Tak | Ścieżka w kontenerze, w którym powinien zostać zainstalowany wolumin. Nie może zawierać dwukropka (:). |
|  Readonly | boolean | Nie | Flaga wskazująca, czy instalacji woluminu jest tylko do odczytu. |




### <a name="containerprobe-object"></a>ContainerProbe, obiekt

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  Exec | object | Nie | Wykonanie polecenia sondowania — [obiekt ContainerExec](#containerexec-object) |
|  httpGet | object | Nie | Ustawienia http get sondy — [obiekt ContainerHttpGet](#containerhttpget-object) |
|  initialDelaySeconds | liczba całkowita | Nie | Sekundy początkowego opóźnienia. |
|  periodSeconds | liczba całkowita | Nie | Okres w sekundach. |
|  failureThreshold | liczba całkowita | Nie | Próg awarii. |
|  successThreshold | liczba całkowita | Nie | Próg sukcesu. |
|  limit czasuSekundy | liczba całkowita | Nie | Limit czasu w sekundach. |




### <a name="resourcerequests-object"></a>ResourceRequests, obiekt

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | liczba | Tak | Żądanie pamięci w GB tego wystąpienia kontenera. |
|  Procesora | liczba | Tak | Żądanie procesora CPU tego wystąpienia kontenera. |
|  Gpu | object | Nie | Żądanie procesora GPU tego wystąpienia kontenera. - [Obiekt GpuResource](#gpuresource-object) |




### <a name="resourcelimits-object"></a>ResourceLimits, obiekt

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | liczba | Nie | Limit pamięci w GB tego wystąpienia kontenera. |
|  Procesora | liczba | Nie | Limit procesora CPU tego wystąpienia kontenera. |
|  Gpu | object | Nie | Limit procesora GPU tego wystąpienia kontenera. - [Obiekt GpuResource](#gpuresource-object) |




### <a name="containerexec-object"></a>ContainerExec, obiekt

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  command | array | Nie | Polecenia do wykonania w kontenerze. - ciąg |




### <a name="containerhttpget-object"></a>ContainerHttpGet, obiekt

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  path | ciąg | Nie | Ścieżka do sondy. |
|  port | liczba całkowita | Tak | Numer portu do sondowania. |
|  schemat | enum | Nie | Schemat. — http lub https |




### <a name="gpuresource-object"></a>Obiekt GpuResource

|  Nazwa | Typ | Wymagane | Wartość |
|  ---- | ---- | ---- | ---- |
|  count | liczba całkowita | Tak | Liczba zasobów procesora GPU. |
|  sku | enum | Tak | SKU zasobu procesora GPU. - K80, P100, V100 |


## <a name="next-steps"></a>Następne kroki

Zobacz samouczek [Deploy a multi-container group using a YAML file](container-instances-multi-container-yaml.md)(Wdrażanie grupy z wieloma kontenerami przy użyciu pliku YAML).

Zobacz przykłady użycia pliku YAML do wdrażania grup kontenerów w sieci [wirtualnej lub](container-instances-vnet.md) [instalacji woluminu zewnętrznego.](container-instances-volume-azure-files.md)

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
