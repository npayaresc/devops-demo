# DevOps Demo Agent

This is a sample showing how to onboard an agent and upload data (data points,
events, files).

The demo is based on the starter code generated by the
[mindconnect-nodejs library](https://github.com/mindsphere/mindconnect-nodejs)
and uses this library for connectivity.

## Prerequisites

It's required to create two assets in the MindSphere Asset Manager with the
[same configuration as the sample in mindconnect-nodejs](https://github.com/mindsphere/mindconnect-nodejs#step-0-create-an-asset-type-and-aspect-types)
for this to work:

```
AspectType 'Environment'
  Variables
    'Humidity'    INT    %
    'Pressure'    DOUBLE kPA
    'Temperature' DOUBLE ºC

AspectType 'Vibration'
  Variables
    'Acceleration' DOUBLE mm/s^2
    'Displacement' DOUBLE mm
    'Frequency'    DOUBLE Hz
    'Velocity'     DOUBLE mm/s

AssetType 'TestAssetType'
  Aspects
    'EnvironmentData': Type 'Environment'
    'VibrationData': Type 'Vibration'

Asset 'TestAsset'
  Type: 'TestAssetType'

Asset 'TestAgent'
  Type: 'MindConnect Lib'
```

- The first Asset is the actual instantiation of the defined Aspects + Aspect
  Types, the type will be whatever Asset Type you defined earlier in the
  Asset Manager
- The second Asset is the Agent, and must be of type `core.mclib`. We need to
  define all the Aspects again in the agent, and then need to map them to the
  actual values in the first Asset

1. Create the Aspect Types with their Variables
1. Create an Asset Type that references the Aspect Types you created earlier
1. Create the first Asset with type the Asset Type created earlier
1. Create the second Asset (the Agent) with type `MindConnect Lib`:
    - Choose a security profile and generate the onboarding key json
    - Copy the key data, you'll need it later for the `agentconfig.json` file
1. Configure the Agent:
    - In the *Configuration* tab, creating a Data Source for each of the Aspect
      Types instantiated earlier, adding inside Data Points matching each of the
      Variables of the Aspect Type (the name can be chosen freely, but the Data
      Type and Unit must match)
    - Then in the *Data mappings* tab, link each of the variables (Data Points)
      to the Aspects of the Asset. You'll have to click the *Link variable* link
      and then search for the Asset. Please note that you will only be able to
      link Data Points that are compatible (type and units match)

After this you will have an Agent linked to the Asset, and you will be able to
deliver data points to the Asset via the Agent.

## How to run

1. Install the dependencies
    ```sh
    yarn install
    ```
1. Download the [initial JSON token of your agent](https://developer.mindsphere.io/howto/howto-agent-onboard.html#getting-the-boarding-configuration)
    and save it as `agentconfig.json`. If you prefer to do this manually,
    you can use `agentconfig.sample.json` as a template
1. Copy the file `assetmanager.sample.json` to `assetmanager.json` and adapt the
    identifiers of the Asset and the Data Points. You can find them in the Asset
    and Agent configurations of the MindSphere Asset Manager
1. Run the code. The script will perform the agent onboarding and upload
    timeseries data, create an event and upload a file
    ```sh
    yarn start
    ```
1. Now you can go to the MindSphere Fleet Manager to view the uploaded data

## Notes

The timeseries data in MindSphere can be assigned to either the Agent or the
Assets. There's use cases in which it is useful to map from a single agent to
one or multiple assets, e.g. when you have an environment temperature sensor
data and you want to assign that timeseries data to all the assets which are
in the room.

The files and the events don't require mappings and they can be assigned
directly to the assets.

It is in most cases the best option to keep everything in the actual Asset and
not to upload any data to the Agent. The Agent has other interesting data
though, like the *Online Status*

## TODOs

- Perform the Agent onboarding automatically via API calls to the
  *Agent Management Service*