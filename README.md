﻿# FI.API Sign Tool

.NET Core tool to help with signing payloads correctly

This sample ClearBank® code is intended to provide Financial Institutions examples to help integrate with ClearBank®’s live API.
All information provided by ClearBank® is provided "as is" and without any implied warranty, representation, condition or otherwise, regarding its accuracy or completeness.

## Description

This tool is designed to replicate the steps that an FI.API client needs to take to sign a payload when making a POST request to the FI.API.

It provides diagnostic output at each stage so users can verify against their own client solutions and be confident their implementations are functionally correct.

## Availability

Packaged as C# source code this is targeted at .NET Core, but is compatible with any OS capable of running .NET Core (e.g. Windows, MacOS, Linux). Specifically, you will need .NET Core v2.1 installed.

Contact ClearBank support if you need a pre-built executable for your OS.

From the project directory, it can be called using:

``` cmd
dotnet run -- [command] [arguments]
```

## Operation

There are 3 steps to getting a Digital Signature header from a payload:

- `Hash` - The input payload is UTF8 encoded and a SHA256 hash generated against that encoded data.
- `Sign` - The SHA256 encoded data is then signed using a Private Key.
- `Encode` - The signature is then Base64 encoded for unambiguous transportion over HTTP(s).

This tool can invoke any step individually or all 3 in a chain.

## Quickstart

```cmd
REM Hash Sign and Encode data from the command line using KeyVault
dotnet run -- HashSignEncode -d "{ payload data }" -p AzureKeyVault -v "Url=https://my.vault.azure.net;KeyName=MyKeyName;ClientId=xxxxx;ClientSecret=yyyyy"

REM Hash Sign and Encode data from a filename using a local Private Key
dotnet run -- HashSignEncode -f [filename] -p FileName -k "C:\MyPrivateKey.pem"
```

- -d / -f are interchangeable - input data can come from command line or a file
- -p [provider] - specifies the Signing provider (`FileName` / `AzureKeyVault`)
- -v [connectionstring] - specifies the connection details to Azure KeyVault (`Url=;KeyName=;ClientId=;ClientSecret=;`)

Should produce output similar to:

```text
> dotnet run -- hashsignencode -d "{ \"Message\": \"Hello World\" }" -p AzureKeyvault -v "Url=https://some.vault.azure.net/;Keyname=MyKey;ClientId=MyClientId;ClientSecret=MyClientSecret

RunHash
Using: { "Message": "Hello World" }
UTF8EncodedData: 123,32,34,77,101,115,115,97,103,101,34,58,32,34,72,101,108,108,111,32,87,111,114,108,100,34,32,125
HashedData (SHA256): 148,189,234,202,214,13,8,58,105,172,74,24,83,242,253,161,227,250,250,248,102,40,47,23,103,197,125,115,243,112,62,107

RunSign
Using: 148,189,234,202,214,13,8,58,105,172,74,24,83,242,253,161,227,250,250,248,102,40,47,23,103,197,125,115,243,112,62,107
SignedData: 139,142,96,33,242,90,108,25,219,16,68,130,181,214,92,10,251,194,73,20,76,217,7,190,115,240,67,219,71,216,143,2,97,56,15,114,140,154,1,112,94,43,64,100,11,9,84,116,1,143,140,172,221,199,163,3,234,12,89,108,0,0,93,217,207,168,186,84,174,63,219,158,42,204,70,200,138,127,139,115,179,211,27,243,134,27,125,75,152,147,144,12,9,113,166,111,49,53,123,74,40,99,101,16,254,175,247,205,33,25,67,13,5,57,233,45,102,189,90,92,220,8,179,97,159,58,128,19,75,31,32,5,58,227,7,96,84,239,187,242,235,24,6,33,31,163,167,241,93,16,173,254,1,141,215,160,214,130,60,159,53,75,183,24,116,120,128,244,113,170,170,209,230,41,207,116,177,37,68,252,93,144,54,251,2,65,232,152,1,28,169,137,249,33,33,32,220,201,19,90,211,19,127,32,77,35,79,211,203,171,50,145,251,38,232,238,194,247,16,183,241,135,182,62,68,152,152,40,174,96,1,143,166,165,17,24,,6,52,4,177,111,58,147,59,243,227,201,110,248,165,88,112,107,170,46,16,27,55,245,105,167,17,227,248,170,142,238,48,24,142,198,121,68,200,139,35,5,63,172,75,107,32,17,1,186,83,152,150,119,241,152,117,4,43,130,44,113,125,229,107,132,211,176,155,171,46,197,40,186,236,134,164,235,78,75,212,250,255,150,222,45,128,67,135,47,114,207,136,,208,130,186,67,154,137,218,125,183,76,184,50,202,233,133,254,239,41,112,137,144,150,115,53,215,130,188,90,236,246,213,48,241,218,84,250,32,143,36,63,203,2,38,27,61,127,164,143,244,81,33,215,90,188,237,157,17,211,163,188,94,58,6,72,56,177,84,128,16,168,42,192,115,52,68,194,172,6,175,254,163,50,197,143,83,160,240,122,158,100,96,2,38,201,93,252,101,231,45,17,80,142,35,144,26,232,144,132,212,194,62,3,148,58,219,26,174,15,242,238,76,254,149,225,199,208,247,182,103,222,134,71,82,153,25,40,169,230,41,55,238,204,218,11,154,27,242,51,117,213,8,226,216,178,108,163,241,2,156,84,21,90,226,172,59,28,156,196,39,73,143,71,47,194,196,5,220,229,47,55,200,2,133,38,157,1,71,149,206,78,55

RunEncode
Using: 139,142,96,33,242,90,108,25,219,16,68,130,181,214,92,10,251,194,73,20,76,217,7,190,115,240,67,219,71,216,143,2,97,56,15,114,140,154,1,112,94,43,64,100,119,84,116,1,143,140,172,221,199,163,3,234,12,89,108,0,0,93,217,207,168,186,84,174,63,219,158,42,204,70,200,138,127,139,115,179,211,27,243,134,27,125,75,152,147,144,129,113,166,111,49,53,123,74,40,99,101,16,254,175,247,205,33,25,67,13,5,57,233,45,102,189,90,92,220,8,179,97,159,58,128,19,75,31,32,5,58,227,7,96,84,239,187,242,235,246,33,31,163,167,241,93,16,173,254,1,141,215,160,214,130,60,159,53,75,183,24,116,120,128,244,113,170,170,209,230,41,207,116,177,37,68,252,93,144,54,251,2,65,232,152,128,16,9,137,249,33,33,32,220,201,19,90,211,19,127,32,77,35,79,211,203,171,50,145,251,38,232,238,194,247,16,183,241,135,182,62,68,152,152,40,174,96,1,143,166,165,17,24,6,52,4,177,111,58,147,59,243,227,201,110,248,165,88,112,107,170,46,16,27,55,245,105,167,17,227,248,170,142,238,48,24,142,198,121,68,200,139,35,5,63,172,75,107,32,171,186,83,152,150,119,241,152,117,4,43,130,44,113,125,229,107,132,211,176,155,171,46,197,40,186,236,134,164,235,78,75,212,250,255,150,222,45,128,67,135,47,114,207,136,208,130,186,67,154,137,218,125,183,76,184,50,202,233,133,254,239,41,112,137,144,150,115,53,215,130,188,90,236,246,213,48,241,218,84,250,32,143,36,63,203,2,38,27,61,127,1,64,143,244,81,33,215,90,188,237,157,17,211,163,188,94,58,6,72,56,177,84,128,16,168,42,192,115,52,68,194,172,6,175,254,163,50,197,143,83,160,240,122,158,100,96,238,20,1,93,252,101,231,45,17,80,142,35,144,26,232,144,132,212,194,62,3,148,58,219,26,174,15,242,238,76,254,149,225,199,208,247,182,103,222,134,71,82,153,25,40,169,230,41,5,5,238,204,218,11,154,27,242,51,117,213,8,226,216,178,108,163,241,2,156,84,21,90,226,172,59,28,156,196,39,73,143,71,47,194,196,5,220,229,47,55,200,2,133,38,157,171,14,9,206,78,55

EncodedData: i45gIfJabBnbEESCtdZcCvvCSRRM2Qe+c/BD20fYjwJhOA9yjJoBcF4rQGR3VHQBj4ys3cejA+oMWWwAAF3Zz6i6VK4/254qzEbIin+Lc7PTG/OGG31LmJOQgXGmbzE1e0ooY2UQ/q/3zSEZQw0FOektZr1aXNwIs2GfOoATSx8gBTrjB2BU77vy6/YhH6On8V0Qrf4Bjdeg1oI8nzVLtxh0eID0caqq0eYpz3SxJUT8XZA2+wJB6JiAqYn5ISEg3MkTWtMTfyBNI0/Ty6sykfsm6O7C9xC38Ye2PkSYmCiuYAGPpqURGAY0BLFvOpM78+PJbvilWHBrqi4QGzf1aacR4/iqju4wGI7GeUTIiyMFP6xLayCrulOYlnfxmHUEK4IscX3la4TTsJurLsUouuyGpOtOS9T6/5beLYBDhy9yz4jQgrpDmonafbdMuDLK6YX+7ylwiZCWczXXgrxa7PbVMPHaVPogjyQ/ywImGz1/pI/0USHXWrztnRHTo7xeOgZIOLFUgBCoKsBzNETCrAav/qMyxY9ToPB6nmRg7sld/GXnLRFQjiOQGuiQhNTCPgOUOtsarg/y7kz+leHH0Pe2Z96GR1KZGSip5ik37szaC5ob8jN11Qji2LJso/ECnFQVWuKsOxycxCdJj0cvwsQF3OUvN8gChSadq5XOTjc=
```

This `EncodedData` response can now be used as the `DigitalSignature` header value for that payload, with a corresponding Bearer Token value.

## Reference Example

A sample private and public key pair are included in this sample, to provide a reference. If you can match your implementation results to those here, you should be able to sign your own requests successfully.

```cmd
> dotnet run -- HashSignEncode -d "{ \"Message\": \"Hello World\" }" -k "..\Data\FI.SignTool.Testing.key"

RunHash
Using: { "Message": "Hello World" }
UTF8EncodedData: 123,32,34,77,101,115,115,97,103,101,34,58,32,34,72,101,108,108,111,32,87,111,114,108,100,34,32,125
HashedData (SHA256): 148,189,234,202,214,13,8,58,105,172,74,24,83,242,253,161,227,250,250,248,102,40,47,23,103,197,125,115,243,112,62,107

RunSign
Using: 148,189,234,202,214,13,8,58,105,172,74,24,83,242,253,161,227,250,250,248,102,40,47,23,103,197,125,115,243,112,62,107
SignedData: 88,236,245,56,121,85,215,254,53,165,40,89,201,107,186,50,141,129,5,224,31,110,12,64,28,133,128,180,60,122,9,42,85,137,136,178,108,14,179,55,246,228,5,229,119,78,119,189,32,212,47,158,4,109,200,202,36,93,81,2,64,252,31,190,196,90,224,53,157,234,214,9,197,3,38,219,27,30,51,39,109,218,75,15,179,39,200,138,49,158,152,170,2,76,74,104,26,168,57,102,51,19,30,179,241,244,203,66,108,199,174,169,48,33,148,101,243,92,193,57,255,202,2,238,24,97,29,249,141,171,54,212,229,163,72,37,17,49,117,182,182,255,190,51,210,178,79,59,217,214,243,147,75,18,112,99,107,0,104,114,119,171,66,195,163,231,13,197,1,158,15,172,40,133,158,68,130,39,111,142,179,83,14,199,207,235,250,132,10,31,163,135,9,164,200,56,12,58,226,96,114,1,91,139,144,54,47,167,115,166,55,80,248,183,48,121,10,6,149,85,210,175,173,33,189,135,202,106,232,29,46,236,187,169,164,101,254,175,38,110,31,24,197,224,138,17,77,246,30,135,175,90,245,151,187,184

RunEncode
Using: 88,236,245,56,121,85,215,254,53,165,40,89,201,107,186,50,141,129,5,224,31,110,12,64,28,133,128,180,60,122,9,42,85,137,136,178,108,14,179,55,246,228,5,229,119,78,119,189,32,212,47,158,4,109,200,202,36,93,81,2,64,252,31,190,196,90,224,53,157,234,214,9,197,3,38,219,27,30,51,39,109,218,75,15,179,39,200,138,49,158,152,170,2,76,74,104,26,168,57,102,51,19,30,179,241,244,203,66,108,199,174,169,48,33,148,101,243,92,193,57,255,202,2,238,24,97,29,249,141,171,54,212,229,163,72,37,17,49,117,182,182,255,190,51,210,178,79,59,217,214,243,147,75,18,112,99,107,0,104,114,119,171,66,195,163,231,13,197,1,158,15,172,40,133,158,68,130,39,111,142,179,83,14,199,207,235,250,132,10,31,163,135,9,164,200,56,12,58,226,96,114,1,91,139,144,54,47,167,115,166,55,80,248,183,48,121,10,6,149,85,210,175,173,33,189,135,202,106,232,29,46,236,187,169,164,101,254,175,38,110,31,24,197,224,138,17,77,246,30,135,175,90,245,151,187,184
EncodedData: WOz1OHlV1/41pShZyWu6Mo2BBeAfbgxAHIWAtDx6CSpViYiybA6zN/bkBeV3Tne9INQvngRtyMokXVECQPwfvsRa4DWd6tYJxQMm2xseMydt2ksPsyfIijGemKoCTEpoGqg5ZjMTHrPx9MtCbMeuqTAhlGXzXME5/8oC7hhhHfmNqzbU5aNIJRExdba2/74z0rJPO9nW85NLEnBjawBocnerQsOj5w3FAZ4PrCiFnkSCJ2+Os1MOx8/r+oQKH6OHCaTIOAw64mByAVuLkDYvp3OmN1D4tzB5CgaVVdKvrSG9h8pq6B0u7LuppGX+ryZuHxjF4IoRTfYeh69a9Ze7uA==
```

## Command Line Reference

```cmd
dotnet run -- --help
```

will show the commands supported by the tool.

All operations support input data being supplied on the command line (using -d [data]) or from a filename (using -f [filename])

For functions that require input data as a byte array (Sign, Encode) the data should be specified as comma separted byte values.

E.g. -d 123,117,56,27

### Hash Operation

```text
> dotnet run -- hash --help
FI.API.SignTool 1.0.0
(c) 2019 ClearBank

  -d, --data            The data to be processed

  -f, --datafilename    The filename containing data to be processed

  --help                Display this help screen.

  --version             Display version information.
```

Example:

```cmd
dotnet run -- hash -d "{ \"Message\": \"Hello World\" }"
```

> NOTE: Quotes escaped on command line

should produce:

```text
RunHash
Using: { "Message": "Hello World" }
UTF8EncodedData: 123,32,34,77,101,115,115,97,103,101,34,58,32,34,72,101,108,108,111,32,87,111,114,108,100,34,32,125
HashedData (SHA256): 148,189,234,202,214,13,8,58,105,172,74,24,83,242,253,161,227,250,250,248,102,40,47,23,103,197,125,115,243,112,62,107
```

### Sign Operation

```cmd
> dotnet run -- sign --help
FI.API.SignTool 1.0.0
(c) 2019 ClearBank

  -d, --data            The data to be processed

  -f, --datafilename    The filename containing data to be processed

  -p, --provider        (Default: FileName) The Provider to use for Signing requests (FileName / AzureKeyVault)

  -k, --keyfilename     FileName for Private Key

  -v, --keyvault        Azure KeyVault connection string (Url=[KeyVaultUrl];KeyName=;KeyVersion=;ClientId=;ClientSecret=;)
                                                      OR (Url=[keyIdentifier];ClientId=;ClientSecret=;)

  --help                Display this help screen.

  --version             Display version information.
  ```

Example:

```cmd
dotnet run -- sign -d "148,189,234,202,214,13,8,58,105,172,74,24,83,242,253,161,227,250,250,248,102,40,47,23,103,197,125,115,243,112,62,107" -p AzureKeyvault -v "Url=https://my.vault.azure.net/;Keyname=MyKey;ClientId=aaa;ClientSecret=bbb"
```

might produce:

```text
RunSign
Using: 148,189,234,202,214,13,8,58,105,172,74,24,83,242,253,161,227,250,250,248,102,40,47,23,103,197,125,115,243,112,62,107
SignedData: 139,142,96,33,242,90,108,25,219,16,68,130,181,214,92,10,251,194,73,20,76,217,7,190,115,240,67,219,71,216,143,2,97,56,15,114,140,154,1,112,94,43,64,100,119,84,116,1,143,140,172,221,199,163,3,234,12,89,108,0,0,93,217,207,168,186,84,174,63,219,158,42,204,70,200,138,127,139,115,179,211,27,243,134,27,125,75,152,147,144,129,113,166,111,49,53,123,74,40,99,101,16,254,175,247,205,33,25,67,13,5,57,233,45,102,189,90,92,220,8,179,97,159,58,128,19,75,31,32,5,58,227,7,96,84,239,187,242,235,246,33,31,163,167,241,93,16,173,254,1,141,215,160,214,130,60,159,53,75,183,24,116,120,128,244,113,170,170,209,230,41,207,116,177,37,68,252,93,144,54,251,2,65,232,152,128,169,137,249,33,33,32,220,201,19,90,211,19,127,32,77,35,79,211,203,171,50,145,251,38,232,238,194,247,16,183,241,135,182,62,68,152,152,40,174,96,1,143,166,165,17,24,6,52,4,177,111,58,147,59,243,227,201,110,248,165,88,112,107,170,46,16,27,55,245,105,167,17,227,248,170,142,238,48,24,142,198,121,68,200,139,35,5,63,172,75,107,32,171,186,83,152,150,119,241,152,117,4,43,130,44,113,125,229,107,132,211,176,155,171,46,197,40,186,236,134,164,235,78,75,212,250,255,150,222,45,128,67,135,47,114,207,136,208,130,186,67,154,137,218,125,183,76,184,50,202,233,133,254,239,41,112,137,144,150,115,53,215,130,188,90,236,246,213,48,241,218,84,250,32,143,36,63,203,2,38,27,61,127,164,143,244,81,33,215,90,188,237,157,17,211,163,188,94,58,6,72,56,177,84,128,16,168,42,192,115,52,68,194,172,6,175,254,163,50,197,143,83,160,240,122,158,100,96,238,201,93,252,101,231,45,17,80,142,35,144,26,232,144,132,212,194,62,3,148,58,219,26,174,15,242,238,76,254,149,225,199,208,247,182,103,222,134,71,82,153,25,40,169,230,41,55,238,204,218,11,154,27,242,51,117,213,8,226,216,178,108,163,241,2,156,84,21,90,226,172,59,28,156,196,39,73,143,71,47,194,196,5,220,229,47,55,200,2,133,38,157,171,149,153,39,88
```

### Encode Operation

```cmd
> dotnet run -- encode --help
FI.API.SignTool 1.0.0
(c) 2019 ClearBank

  -d, --data            The data to be processed

  -f, --datafilename    The filename containing data to be processed

  --help                Display this help screen.

  --version             Display version information.
  ```

Example:

```cmd
dotnet run -- encode -d "139,142,96,33,242,90,108,25,219,16,68,130,181,214,92,10,251,194,73,20,76,217,7,190,115,240,67,219,71,216,143,2,97,56,15,114,140,154,1,112,94,43,64,100,119,84,116,1,143,140,172,221,199,163,3,234,12,89,108,0,0,93,217,207,168,186,84,174,63,219,158,42,204,70,200,138,127,139,115,179,211,27,243,134,27,125,75,152,147,144,129,113,166,111,49,53,123,74,40,99,101,16,254,175,247,205,33,25,67,13,5,57,233,45,102,189,90,92,220,8,179,97,159,58,128,19,75,31,32,5,58,227,7,96,84,239,187,242,235,246,33,31,163,167,241,93,16,173,254,1,141,215,160,214,130,60,159,53,75,183,24,116,120,128,244,113,170,170,209,230,41,207,116,177,37,68,252,93,144,54,251,2,65,232,152,128,169,137,249,33,33,32,220,201,19,90,211,19,127,32,77,35,79,211,203,171,50,145,251,38,232,238,194,247,16,183,241,135,182,62,68,152,152,40,174,96,1,143,166,165,17,24,6,52,4,177,111,58,147,59,243,227,201,110,248,165,88,112,107,170,46,16,27,55,245,105,167,17,227,248,170,142,238,48,24,142,198,121,68,200,139,35,5,63,172,75,107,32,171,186,83,152,150,119,241,152,117,4,43,130,44,113,125,229,107,132,211,176,155,171,46,197,40,186,236,134,164,235,78,75,212,250,255,150,222,45,128,67,135,47,114,207,136,208,130,186,67,154,137,218,125,183,76,184,50,202,233,133,254,239,41,112,137,144,150,115,53,215,130,188,90,236,246,213,48,241,218,84,250,32,143,36,63,203,2,38,27,61,127,164,143,244,81,33,215,90,188,237,157,17,211,163,188,94,58,6,72,56,177,84,128,16,168,42,192,115,52,68,194,172,6,175,254,163,50,197,143,83,160,240,122,158,100,96,238,201,93,252,101,231,45,17,80,142,35,144,26,232,144,132,212,194,62,3,148,58,219,26,174,15,242,238,76,254,149,225,199,208,247,182,103,222,134,71,82,153,25,40,169,230,41,55,238,204,218,11,154,27,242,51,117,213,8,226,216,178,108,163,241,2,156,84,21,90,226,172,59,28,156,196,39,73,143,71,47,194,196,5,220,229,47,55,200,2,133,38,157,171,149,153,39,88"
```

might produce:

```text
RunEncode
Using: 139,142,96,33,242,90,108,25,219,16,68,130,181,214,92,10,251,194,73,20,76,217,7,190,115,240,67,219,71,216,143,2,97,56,15,114,140,154,1,112,94,43,64,100,119,84,116,1,143,140,172,221,199,163,3,234,12,89,108,0,0,93,217,207,168,186,84,174,63,219,158,42,204,70,200,138,127,139,115,179,211,27,243,134,27,125,75,152,147,144,129,113,166,111,49,53,123,74,40,99,101,16,254,175,247,205,33,25,67,13,5,57,233,45,102,189,90,92,220,8,179,97,159,58,128,19,75,31,32,5,58,227,7,96,84,239,187,242,235,246,33,31,163,167,241,93,16,173,254,1,141,215,160,214,130,60,159,53,75,183,24,116,120,128,244,113,170,170,209,230,41,207,116,177,37,68,252,93,144,54,251,2,65,232,152,128,169,137,249,33,33,32,220,201,19,90,211,19,127,32,77,35,79,211,203,171,50,145,251,38,232,238,194,247,16,183,241,135,182,62,68,152,152,40,174,96,1,143,166,165,17,24,6,52,4,177,111,58,147,59,243,227,201,110,248,165,88,112,107,170,46,16,27,55,245,105,167,17,227,248,170,142,238,48,24,142,198,121,68,200,139,35,5,63,172,75,107,32,171,186,83,152,150,119,241,152,117,4,43,130,44,113,125,229,107,132,211,176,155,171,46,197,40,186,236,134,164,235,78,75,212,250,255,150,222,45,128,67,135,47,114,207,136,208,130,186,67,154,137,218,125,183,76,184,50,202,233,133,254,239,41,112,137,144,150,115,53,215,130,188,90,236,246,213,48,241,218,84,250,32,143,36,63,203,2,38,27,61,127,164,143,244,81,33,215,90,188,237,157,17,211,163,188,94,58,6,72,56,177,84,128,16,168,42,192,115,52,68,194,172,6,175,254,163,50,197,143,83,160,240,122,158,100,96,238,201,93,252,101,231,45,17,80,142,35,144,26,232,144,132,212,194,62,3,148,58,219,26,174,15,242,238,76,254,149,225,199,208,247,182,103,222,134,71,82,153,25,40,169,230,41,55,238,204,218,11,154,27,242,51,117,213,8,226,216,178,108,163,241,2,156,84,21,90,226,172,59,28,156,196,39,73,143,71,47,194,196,5,220,229,47,55,200,2,133,38,157,171,149,153,39,88
EncodedData: i45gIfJabBnbEESCtdZcCvvCSRRM2Qe+c/BD20fYjwJhOA9yjJoBcF4rQGR3VHQBj4ys3cejA+oMWWwAAF3Zz6i6VK4/254qzEbIin+Lc7PTG/OGG31LmJOQgXGmbzE1e0ooY2UQ/q/3zSEZQw0FOektZr1aXNwIs2GfOoATSx8gBTrjB2BU77vy6/YhH6On8V0Qrf4Bjdeg1oI8nzVLtxh0eID0caqq0eYpz3SxJUT8XZA2+wJB6JiAqYn5ISEg3MkTWtMTfyBNI0/Ty6sykfsm6O7C9xC38Ye2PkSYmCiuYAGPpqURGAY0BLFvOpM78+PJbvilWHBrqi4QGzf1aacR4/iqju4wGI7GeUTIiyMFP6xLayCrulOYlnfxmHUEK4IscX3la4TTsJurLsUouuyGpOtOS9T6/5beLYBDhy9yz4jQgrpDmonafbdMuDLK6YX+7ylwiZCWczXXgrxa7PbVMPHaVPogjyQ/ywImGz1/pI/0USHXWrztnRHTo7xeOgZIOLFUgBCoKsBzNETCrAav/qMyxY9ToPB6nmRg7sld/GXnLRFQjiOQGuiQhNTCPgOUOtsarg/y7kz+leHH0Pe2Z96GR1KZGSip5ik37szaC5ob8jN11Qji2LJso/ECnFQVWuKsOxycxCdJj0cvwsQF3OUvN8gChSadq5WZJ1g=
```
