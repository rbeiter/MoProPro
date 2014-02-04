# MoProPro

Add devices to iPhone mobile provisioning profiles from the command-line!

## Motivation

As an iPhone developer, adding new devices to existing mobile provisioning
profiles is an action you'll regularly have to perform, e.g., when setting up
your beta testing program.

Doing this through Apple's iPhone Developer Program is a tedious task,
involving many mouse clicks (at least 15, if you're not logged in, plus 2 for
every extra device)

This tool alleviates this painful process by offering a simple command line
tool that you run from your project directory.


## Features

- Adding a batch of devices
- Simple YAML configuration and input files
- Device UDID and name validation
- Detects already present devices, but will add them to the profile anyway
- Supports wildcard profiles
- Tries to match the most specific profile
- Checks if a new profile download is pending and tries again (3 times)


## Usage

    Usage: mopropro [options] UDID name
    
    You must provide credentials, either through the command line options
    or in a YAML file (in the current working directory) named '.mopropro'
    
    You can also use stdin to provide multiple device entries
    For example: 'cat devices.yml | mopropro'
    
    This program will look for an Info.plist file in the current directory
    to get the app identifier from (unless you specify --no-provisioning)
    
        -u, --username USERNAME          ADC Username
        -p, --password PASSWORD          ADC Password
            --no-provisioning            Do not create a provisioning profile, only add devices
        -tf, --testflight                When piping in devices from a file, use the TestFlight format instead of YAML
        -v, --verbose                    Be verbose


## Examples

**NOTE**: Execute these commands from your project directory, containing the
`Info.plist` file.


### Simple

    mopropro -u user -p passwd 12345abcde0987654321fedcb0123456789abcde "Name for device"


### Using a config file

For the same result as above, but without having to type your password, create
a YAML `.mopropro` file in the current working directory with

    username: user
    password: passwd

And run
    
    mopropro 12345abcde0987654321fedcb0123456789abcde "Name for device"


### Adding multiple devices

Create a YAML file with device entries

    12345abcde0987654321fedcb0123456789abcde: Name for device
    abcde0987654321fedcb0123456789abcde12345: Second device
    0987654321fedcb0123456789abcde12345abcde: Third device

And pipe it through MoProPro

    cat devices.yml | mopropro -u user -p passwd

You can pipe in a TestFlight format device export using the -tf option

    cat testflight_devices.txt | mopropro -u user -p passwd -tf

### Verbose

Example of verbose output

    Validating input... Ok.
    Getting App Id... com.example.app
    Logging in... Ok.
    Looking up existing devices... 
     - Warning: Device UDID abcde0987654321fedcb0123456789abcde12345 already exists, with name 'Test'.
    Done.
    Adding 2 devices... Submitted.
    Looking up provisioning profile... 
     - Found 5 profiles
     - Found 2 matching profiles
     - Found Ad Hoc profile
     - Adding devices
    Saving profile with new devices... Ok.
    Waiting a bit while the new profile is generated... Ok.
    Trying to retrieve new profile... Got it!
    Saved new provisioning profile to 'Example_Ad_Hoc.mobileprovision'
