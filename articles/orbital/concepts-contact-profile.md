---
title: Ground station contact profile - Azure Orbital
description: Learn more about the contact profile object, including how to create, modify, and delete the profile.
author: hrshelar
ms.service: orbital
ms.topic: conceptual
ms.custom: ga
ms.date: 07/13/2022
ms.author: hrshelar
#Customer intent: As a satellite operator or user, I want to understand how to use the contact profile so that I can take passes using the Azure Orbital Ground Station (AOGS) service.
---

# Ground station contact profile

The contact profile object stores pass requirements such as links and endpoint details for each link. Use this object with the spacecraft object at time of scheduling to view and schedule available passes.

You can create many contact profiles to represent different types of passes depending on your mission operations. For example, you can create a contact profile for a command and control pass or a contact profile for a downlink only pass. 

These objects are mutable and don't undergo an authorization process like the spacecraft objects do. One contact profile can be used with many spacecraft objects. 

See [how to configure a contact profile](contact-profile.md) for the full list of parameters.

## Prerequisites 

- Subnet that is created in the VNET and resource group you desire. See [Prepare network for Azure Orbital Ground Station integration.](prepare-network.md)

## Creating a contact profile 

Follow the steps in [how to create a contact profile.](contact-profile.md). 

## Adjusting pass parameters

Specify a minimum pass time to ensure passes of a certain duration. Specify a minimum elevation to ensure passes above a certain elevation.

The two parameters above are used by the service during the contact scheduling. Avoid changing these on a pass-by-pass basis and create multiple contact profiles if you need such flexibility. 

At the moment autotrack is disabled and autotracking options are not applied.

## Understanding links and channels

A whole band, unique in direction, and unique in polarity is called a link. Channels, which are children under links, specify center frequency, bandwidth, and endpoints. Typically there's only one channel per link but some applications require multiple channels per link. Refer to the Ground Station manual for a full list of supported bands and antenna capabilities.

You can specify an EIRP and G/T requirement for each link. EIRP applies to uplinks and G/T applies to downlinks. You can give a name to each link and channel to keep track of these properties.

Look at the example below to see how to specify an RHCP channel and an LHCP channel if your mission requires dual-polarization on downlink.  

```json
{
  "location": "eastus2",
  "tags": null,
  "id": "/subscriptions/c1be1141-a7c9-4aac-9608-3c2e2f1152c3/resourceGroups/contoso-Rgp/providers/Microsoft.Orbital/contactProfiles/CONTOSO-CP",
  "name": "CONTOSO-CP",
  "type": "Microsoft.Orbital/contactProfiles",
  "properties": {
    "provisioningState": "Succeeded",
    "minimumViableContactDuration": "PT1M",
    "minimumElevationDegrees": 5,
    "autoTrackingConfiguration": "disabled",
    "eventHubUri": "/subscriptions/c1be1141-a7c9-4aac-9608-3c2e2f1152c3/resourceGroups/contoso-Rgp/providers/Microsoft.EventHub/namespaces/contosoHub/eventhubs/contosoHub",
    "networkConfiguration": {
      "subnetId": "/subscriptions/c1be1141-a7c9-4aac-9608-3c2e2f1152c3/resourceGroups/contoso-Rgp/providers/Microsoft.Network/virtualNetworks/contoso-vnet/subnets/orbital-delegated-subnet"
    },
    "links": [
      {
        "name": "contoso-downlink-rhcp",
        "polarization": "RHCP",
        "direction": "downlink",
        "gainOverTemperature": 25,
        "eirpdBW": 0,
        "channels": [
          {
            "name": "contoso-downlink-channel-rhcp",
            "centerFrequencyMHz": 8160,
            "bandwidthMHz": 15,
            "endPoint": {
              "ipAddress": "10.1.0.5",
              "endPointName": "ContosoTest_Downlink_RHCP",
              "port": "51103",
              "protocol": "UDP"
            },
            "modulationConfiguration": null,
            "demodulationConfiguration": null,
            "encodingConfiguration": null,
            "decodingConfiguration": null
          }
        ]
      }
      {
        "name": "contoso-downlink-lhcp",
        "polarization": "LHCP",
        "direction": "downlink",
        "gainOverTemperature": 25,
        "eirpdBW": 0,
        "channels": [
          {
            "name": "contoso-downlink-channel-lhcp",
            "centerFrequencyMHz": 8160,
            "bandwidthMHz": 15,
            "endPoint": {
              "ipAddress": "10.1.0.5",
              "endPointName": "ContosoTest_Downlink_LHCP",
              "port": "51104",
              "protocol": "UDP"
            },
            "modulationConfiguration": null,
            "demodulationConfiguration": null,
            "encodingConfiguration": null,
            "decodingConfiguration": null
          }
        ]
      }
    ]
  }
}
```


## Applying modems or bring your own

We recommend taking advantage of Orbital's GSaaS software modem functionality if possible. This modem is managed by the service and is inserted between your endpoint and the incoming or outgoing virtual RF stream per channel. We have a library of modems that will be available in the marketplace for you to utilize. If there is no modem that can be used with your application then utilize the virtual RF delivery feature to bring your own modem.

There are 4 parameters related to modem configurations. The table below shows you how to configure these parameters.

| Parameter                 | Options                                                                     |
|---------------------------|-----------------------------------------------------------------------------|
| modulationConfiguration   | 1. Null for virtual RF<br />2. JSON escaped modem config for software modem |
| demodulationConfiguration | 1. Null for virtual RF<br />2. JSON escaped modem config for software modem |
| encodingConfiguration     | Null (not used)                                                             |
| decodingConfiguration     | Null (not used)                                                             |

Use the same modem config file in uplink and downlink channels for full-duplex communications in the same band.

The modem config should be a JSON escaped raw save file from a software modem. Please see the marketplace for modem options.

## Modifying or deleting a contact profile

You can modify or delete the contact profile via the Portal or through the API.

## Configuring contact profile for third party ground stations

When you onboard a third party network, you'll receive a token that identifies your profile. Use this token in the object to link a contact profile to the third party network.

## Next steps

- [Schedule a contact](schedule-contact.md)
- [Update the Spacecraft TLE](update-tle.md)

