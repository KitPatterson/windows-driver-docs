---
title: Code Example for Initializing Device Properties
description: Code Example for Initializing Device Properties
ms.date: 04/20/2017
---

# Code Example for Initializing Device Properties


During the [**IWiaMiniDrv::drvInitItemProperties**](/windows-hardware/drivers/ddi/wiamindr_lh/nf-wiamindr_lh-iwiaminidrv-drvinititemproperties) call for the Root item, the minidriver must initialize the following WIA properties that describe the device:

[**WIA\_DPS\_SERVICE\_ID**](./wia-dps-service-id.md)

[**WIA\_DPS\_DEVICE\_ID**](./wia-dps-device-id.md)

[**WIA\_DPS\_GLOBAL\_IDENTITY**](./wia-dps-global-identity.md)

[**WIA\_DPA\_FIRMWARE\_VERSION**](./wia-dpa-firmware-version.md)

The following code example shows how to initialize WIA\_DPS\_SERVICE\_ID by using the **OpenProperyStore** and **ReadDeviceProperty** methods to read PKEY\_PNPX\_ServiceId. The same general method can be used to initialize each of the device properties.

```cpp
HRESULT hr = S_OK;
IPropertyStore *pPropertyStore = NULL;
BSTR bstrPropertyValue = NULL;

//
// Open the current Property Store and keep it opened until all needed properties are read
//
if (SUCCEEDED(hr))
{
    hr = OpenPropertyStore(&pPropertyStore);
    if (FAILED(hr))
    {
        WIAS_ERROR((g_hInst, "Failed to open the Property Store for the current Function Instance, hr = 0x%08X", hr));
    }
}

//
// Initialize WIA_DPS_SERVICE_ID
//
if (SUCCEEDED(hr))
{
    hr = ReadDeviceProperty(pPropertyStore, &PKEY_PNPX_ServiceId, &bstrPropertyValue);
    if (FAILED(hr))
    {
        WIAS_ERROR((g_hInst, "Failed to read the PKEY_PNPX_ServiceId device property, hr = 0x%08X", hr));
    }

    if ((SUCCEEDED(hr)) && (bstrPropertyValue)) 
    {
        WIAS_TRACE((g_hInst, "Service id: %ws", (LPWSTR)bstrPropertyValue));
 
        hr = AddProperty(WIA_DPS_SERVICE_ID, WIA_DPS_SERVICE_ID_STR, RN, bstrPropertyValue);
        if (FAILED(hr)) 
        {
            WIAS_ERROR((g_hInst, "Failed to add WIA_DPS_SERVICE_ID property to the property manager, hr = 0x%08X", hr));
        }
    }

    if (bstrPropertyValue)
    {
        SysFreeString(bstrPropertyValue);
        bstrPropertyValue = NULL;
    }
}

//
// Repeat the same procedure for WIA_DPS_DEVICE_ID, WIA_DPS_GLOBAL_IDENTITY, and WIA_DPS_FIRMWARE_VERSION
//

//
// Close the Property Store
//
if (pPropertyStore)
{
    pPropertyStore->Release();
    pPropertyStore = NULL;
}
```

 

