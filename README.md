# DoubleVerify Viewability Integration

This repository provides a guide for integrating an DoubleVerify Viewability Pixel.

>[!NOTE]  
>This product is not officially supported. The following information is part of a workaround provided by Equativ's service teams. Each integration case may require individual treatment based on unique requirements.

---

## Context

DoubleVerify (DV) provides clients with the ability to measure both impressions and viewability through the integration of several scripts and elements.  
These elements must be integrated at Equativ's insertion/creative level to ensure accurate measurement.

## Overview

To proceed with the integration, IAS typically provides two main items:

1. A unique DV library, usually referred to as `dvbm.js`.
2. A no-script pixel used as a backup, usually referred to as `visit.jpg`.

Here is an example of the code originally provided by DV:
```html
<!-- Third-part pixel -->
<script src="https://cdn.doubleverify.com/dvbm.js#params"></script>
<noscript><img src="https://tps.doubleverify.com/visit.jpg?params" width="0" height="0"/></noscript>
```

Equativ's Customized Script feature provides a space where this code can be integrated. However, due to the different HTML scopes involved, several steps must be followed for the scripts to correctly read the creative container and position.

---

## Integration

### 1. Ad Container Reference

During configuration, DV requires referencing the container that needs measurement.

Determine what is the Id reference that will be required by reviewing the ad container. By default, the ad container reference will be `sas_formatId` (also included as a macro like `[sas_tagId]`).

DV will use the container's information to reference the proper container. This will be included on the script and backup pixel URLs as value for `btreg` and `btadsrv` parameters, like so:

```html
<script src="https://cdn.doubleverify.com/dvbm.js#params(...)&btreg=[sas_tagId]&btadsrv=[sas_tagId]"></script>
<noscript><img src='https://tps.doubleverify.com/visit.jpg?params(...)&btreg=[sas_tagId]&btadsrv=[sas_tagId]' BORDER='0' WIDTH='1' HEIGHT='1' ALT=''/></noscript>
```

---

### 2. Main Integration

#### Script Library Integration

The following code was created to simplify the integration of the code into the Custom Script at the insertion level.

As a client, the only information that needs to be specified is the script URL. A dedicated variable will gather this information.

Example of reference:
```javascript
// Set Script URL information
let scriptURL = 'https://cdn.doubleverify.com/dvbm.js#params';

```

##### NoScript Integration

In addition DV provides a `noscript` snippet for integrating a Backup Pixel.

This portion of the code should be kept as provided.

```html
<noscript><img src="https://tps.doubleverify.com/visit.jpg?params" alt=""></noscript>
```

##### Complete Code

The example below shows how the final DV code integration should be structured.

A custom logic was developed to ensure the script runs within the correct scope of the page.

The code is minified to simplify the integration process within Equativ's platform.

```html
<!-- DV CUSTOM SCRIPT REFERENCE -->
<script>
    // Set Script URL information
    let scriptURL = 'https://cdn.doubleverify.com/dvbm.js#params(...)&btreg=[sas_tagId]&btadsrv=[sas_tagId]';

    // Function will create the script at the creative scope level - No need to be modified
    !function(){let t=window.parent===window.top?window.top:window.parent;if(!t.document){console.error("dv-custom-target","document-scope-error");return}let e=t.document.createElement("script");e.setAttribute("src",scriptURL),e.async=!0;let o=t.document.body;t.document.querySelector("#[sas_tagId]")?(t.document.querySelector("#[sas_tagId]").appendChild(e),console.log("dv-custom-target","format")):o?(o.appendChild(e),console.log("dv-custom-target","body")):console.error("dv-custom-target","not-found")}();
</script>

<!-- MAINTAIN NO SCRIPT -->
<noscript>
    <img src='https://tps.doubleverify.com/visit.jpg?params(...)&btreg=[sas_tagId]&btadsrv=[sas_tagId]' BORDER='0' WIDTH='1' HEIGHT='1' ALT=''/>
</noscript>
```
> [!NOTE]
> See also the complete version of the code at `dv-custom-script-standard.html` file attached to this repository.

---

### 3. Specific Scenarios
Equativ does not provide a default container reference in the following scenarios; therefore, this configuration must consider several details.

#### Interstitial Ad Integration

Interstitial Ads are not displayed directly into Equativ's default container given the display is required to take into account the whole screen size.

In this particular case, the container id that needs to be referenced is: `sas-interstitial`.

The code should include the change on the `btreg` and `btadsrv` parameters replacing `[sas_tagId]` macro.

```javascript
let scriptURL = 'https://cdn.doubleverify.com/dvbm.js#params(...)&btreg=sas-interstitial&btadsrv=sas-interstitial';
```

> [!NOTE]
> See the complete code of this use case at `dv-custom-script-interstitial.html` file attached to this repository.
---

## Disclaimer

> [!IMPORTANT]  
> Third-party integrations must be tested in a controlled environment before using them as part of the Equativ's creative configuration. It is recommended to contact with IAS if it does not work as expected to improve and optimize this integration.
> Ensure compliance with privacy regulations (e.g., GDPR, CCPA) when handling user data, and confirm that the integration aligns with the [official Custom Script feature documentation](https://help.smartadserver.com/s/article/Configuring-creatives#:~:text=unchecked%20by%20default.-,Custom%20script%20(for%20creatives),-You%20can%20add).

> [!WARNING]  
> External scripts can introduce security risks or performance degradation if not implemented properly.
> Equativ is only responsible for executing the Custom Script feature. Any actions performed during the script execution itself are not the responsibility of Equativ.