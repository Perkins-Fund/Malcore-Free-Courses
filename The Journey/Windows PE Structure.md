<p align="center">
    <img src="../.github/winpe_struct_logo.png">
</p>

**Shameless plug**

This course is given to you for free by The Perkins Cybersecurity Educational Fund: [https://perkinsfund.org/](https://perkinsfund.org/) in collaboration with the Malcore team: [https://m4lc.io/courses/register](https://m4lc.io/courses/register)

Please consider donating to [The Perkins Cybersecurity Educational](https://donorbox.org/malware-bible-fund) Fund and registering for Malcore. You can also join the Malcore Discord server here: [https://m4lc.io/courses/discord](https://m4lc.io/courses/discord)

Malcore offers free threat intel in our Discord via their custom designed Discord bot. Join the Discord to discuss this course in further detail or to ask questions.

You can also support The Perkins Cybersecurity Educational Fund by buying them a coffee

[!["Buy Me A Coffee"](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://ko-fi.com/perkinsfund)

---

# What will be covered?
- [What is a PE file?](#what-is-a-windows-pe-file)
- [Detailed structure breakdown](#detailed-pe-structure-breakdown)
  - [Header](#dos-header)
  - [Stub](#dos-stub)
  - [Signature](#pe-signature)
  - [COFF](#coff-file-header)
  - [OptionalHeader](#optionalheader)
  - [Data Directory](#data-directory)
  - [Sections](#sections)
- [That's it](#thats-all-there-is-to-it)

---

# What is a Windows PE file?

The Windows Portable Executable (PE) file format is a structure used by Windows binary files. It is derived from the Common Object File Format (COFF) used in Unix systems; it is fundamental for Windows systems.

In this course we will break down the Windows PE structure thoroughly. 

**NOTE**: To help visualize some of this information you can see a full analysis of this file done by Malcore here: [https://m4lc.io/course/winpe/full](https://m4lc.io/course/winpe/full)

---

# PE structure visualization

```shell
 ───────────────────────────────────────────────────────────────────
 00000000: 4d5a 9000 0300 0000 0400 0000 ffff 0000  MZ.............. ─┐ 
 00000010: b800 0000 0000 0000 4000 0000 0000 0000  ........@.......  │── DOS Header
 00000020: 0000 0000 0000 0000 0000 0000 0000 0000  ................  │
 00000030: 0000 0000 0000 0000 0000 0000 f000 0000  ................ ─┘ 
 ───────────────────────────────────────────────────────────────────
 ───────────────────────────────────────────────────────────────────
 00000040: 0e1f ba0e 00b4 09cd 21b8 014c cd21 5468  ........!..L.!Th ─┐
 00000050: 6973 2070 726f 6772 616d 2063 616e 6e6f  is program canno  │─ DOS Stub
 00000060: 7420 6265 2072 756e 2069 6e20 444f 5320  t be run in DOS   │
 00000070: 6d6f 6465 2e0d 0d0a 2400 0000 0000 0000  mode....$....... ─┘
 ───────────────────────────────────────────────────────────────────
 ───────────────────────────────────────────────────────────────────
 000000f0: 5045 0000 4c01 0500 b1c6 a062 0000 0000  PE..L......b.... ─┐
 00000100: 0000 0000 e000 0201 0b01 0e1d 0010 0000  ................  │─ PE Header
 00000110: 0016 0000 0000 0000 e613 0000 0010 0000  ................  │
 00000120: 0020 0000 0000 4000 0010 0000 0002 0000  . ....@.........  │
 00000130: 0600 0000 0000 0000 0600 0000 0000 0000  ................  │
 00000140: 0060 0000 0004 0000 0000 0000 0300 4081  .`............@.  │
 00000150: 0000 1000 0010 0000 0000 1000 0010 0000  ................  │
 00000160: 0000 0000 1000 0000 0000 0000 0000 0000  ................  │
 00000170: 8c26 0000 a000 0000 0040 0000 e001 0000  .&.......@......  │
 00000180: 0000 0000 0000 0000 0000 0000 0000 0000  ................  │
 00000190: 0050 0000 7401 0000 e821 0000 7000 0000  .P..t....!..p...  │
 000001a0: 0000 0000 0000 0000 0000 0000 0000 0000  ................  │
 000001b0: 0000 0000 0000 0000 5822 0000 4000 0000  ........X"..@...  │
 000001c0: 0000 0000 0000 0000 0020 0000 c800 0000  ......... ......  │
 000001d0: 0000 0000 0000 0000 0000 0000 0000 0000  ................ ─┘
 ───────────────────────────────────────────────────────────────────
 ───────────────────────────────────────────────────────────────────
 000001e0: 0000 0000 0000 0000 2e74 6578 7400 0000  .........text... ─┐
 000001f0: 110e 0000 0010 0000 0010 0000 0004 0000  ................  │───── Sections
 00000200: 0000 0000 0000 0000 0000 0000 2000 0060  ............ ..`  │
 00000210: 2e72 6461 7461 0000 340c 0000 0020 0000  .rdata..4.... ..  │
 00000220: 000e 0000 0014 0000 0000 0000 0000 0000  ................  │
 00000230: 0000 0000 4000 0040 2e64 6174 6100 0000  ....@..@.data...  │
 00000240: 8803 0000 0030 0000 0002 0000 0022 0000  .....0......."..  │
 00000250: 0000 0000 0000 0000 0000 0000 4000 00c0  ............@...  │
 00000260: 2e72 7372 6300 0000 e001 0000 0040 0000  .rsrc........@..  │
 00000270: 0002 0000 0024 0000 0000 0000 0000 0000  .....$..........  │
 00000280: 0000 0000 4000 0040 2e72 656c 6f63 0000  ....@..@.reloc..  │
 00000290: 7401 0000 0050 0000 0002 0000 0026 0000  t....P.......&..  │
 000002a0: 0000 0000 0000 0000 0000 0000 4000 0042  ............@..B  │
 00000400: b878 3340 00c3 cccc cccc cccc cccc cccc  .x3@............  │
 00000410: b870 3340 00c3 cccc cccc cccc cccc cccc  .p3@............  │
 00000420: 558b ec83 e4f8 5156 8b75 086a 01ff 15bc  U.....QV.u.j....  │
 00000430: 2040 0083 c404 8d4d 0c51 6a00 5650 e8bd   @.....M.Qj.VP..  │
 00000440: ffff ffff 7004 ff30 ff15 b820 4000 83c4  ....p..0... @...  │
 00000450: 185e 8be5 5dc3 cccc cccc cccc cccc cccc  .^..]........... ─┘
 ──────────────────────────────────────────────────────────────────── 
```

---

# Detailed PE structure breakdown

## DOS Header
  - The purpose of the DOS header is to maintain backwards compatibility with older systems. It is a remnant of the DOS era, and tells the system that this is an executable in DOS while providing a pointer to the PE header where modern executable information starts.
    - `e_magic`: The signature that the system recognizes as a valid DOS executable
      - Offset: `0x0000`
    - `e_lfanew`: The pointer to the PE header location
      - Offset: `0x003C`

![e_magic](../.github/winpe/header_1.png)

![e_lfanew](../.github/winpe/header_2.png)

---

## DOS Stub
  - Offset: `0x0040`
    - Small program that runs in DOS mode, usually contains the message: `This program cannot be run in DOS mode.`

![DOS Stub](../.github/winpe/header_3.png)

---

## PE Signature
  - Offset is specified by `e_lfanew` header.
    - Marks the start of the PE file
      - For example if `e_lfanew` is `0x000080` the signature is at `0x80`
      - The signature in bytes is always: `\x50\x45\x00\x00` and displayed in ASCII it is always: `PE\0\0`

![PE Signature](../.github/winpe/header_4.png)

---

## COFF File Header
  - Offset is typically directly after the PE signature
  - Contains general metadata about the file

| Offset | Size (Bytes) | Field Name               | Description                                                        |
|--------|--------------|--------------------------|--------------------------------------------------------------------|
| 0x00   | 2            | **Machine**              | Indicates the architecture.                                        |
| 0x02   | 2            | **NumberOfSections**     | The number of sections in the PE file.                             |
| 0x04   | 4            | **TimeDateStamp**        | Timestamp indicating when the file was created or last modified.   |
| 0x08   | 4            | **PointerToSymbolTable** | The file offset to the symbol table.                               |
| 0x0C   | 4            | **NumberOfSymbols**      | The number of entries in the symbol table.                         |
| 0x10   | 2            | **SizeOfOptionalHeader** | The size of the `Optional Header`.                                 |
| 0x12   | 2            | **Characteristics**      | Flags that indicate the attributes and characteristics of the file |

1. Machine
   - **Offset**: `0x00`
   - **Description**: Indicates the target architecture of the file.
   - **Possible Values**:
       - `0x014C` – Intel 386 (x86).
       - `0x0200` – Intel Itanium.
       - `0x8664` – x64 (AMD64).
       - `0x01C0` – ARM.
       - `0x01C4` – ARMv7.
       - `0xAA64` – ARM64.
       - `0x0100` – MIPS R3000.
   - **NOTE**: There may be more values, we just couldn't think of any other ones

![Machine](../.github/winpe/header_5.png)

2. Number of Sections
   - **Offset**: `0x02`
   - **Description**: The number of section the PE file (counts the section header not the full section).
  
![Number of Sections](../.github/winpe/header_6.png)

3. Timestamp
   - **Offset**: `0x04`
   - **Description**: Contains the timestamp of the file creation or last modified time.
   - **Usage**: This can be used to analyze when a PE file was compiled or built.
   - You can see a timestamp after compilation being retrieved from the file here: [https://m4lc.io/course/winpe/timestamp](https://m4lc.io/course/winpe/timestamp)

![Timestamp](../.github/winpe/header_7.png)

4. PointerToSymbolTable
   - **Offset**: `0x08`
   - **Description**: Points to the start of the symbol table in the file.
       - In executables and DLL files, this field is usually set to `0x00000000`.

![PointerToSymbolTable](../.github/winpe/header_8.png)

5. Number of Symbols
   - **Offset**: `0x0C`
  - **Description**: The number of entries in the symbol table. Is usually set to `0x00000000`.

![Number of Symbols](../.github/winpe/header_9.png)

6. SizeOfOptionalHeader
   - **Offset**: `0x10`
   - **Description**: Specifies the size of the `Optional Header`.
       - For 32-bit files, the size is usually `0x00E0` (224 bytes).
       - For 64-bit files, the size is usually `0x00F0` (240 bytes).

![SizeOfOptionalHeader](../.github/winpe/header_10.png)

7. Characteristics
   - **Offset**: `0x12`
   - **Description**: Contains flags that describe the attributes of the PE file (such as if it's an exe or a DLL).
   - **Possible Values**:
       - `0x0002` – Executable image.
       - `0x0020` – Application can handle addresses larger than 2GB (large address aware).
       - `0x0100` – The file is a DLL.
       - `0x2000` – The file is a system file.
       - `0x4000` – File is a dynamically loadable driver.
       - `0x8000` – File is removable media-aware.

![Characteristics](../.github/winpe/header_11.png)

---

## OptionalHeader

Despite its name this is required for executable images and DLL files. It contains a bunch of information that is needed to properly load the PE file and execute the file in memory. You can see a lot of this information pulled out of a real file and visualize it here: [https://m4lc.io/course/winpe/info](https://m4lc.io/course/winpe/info)

| Offset (PE32) | Offset (PE32+) | Size | Field Name                      | Description                                                                                                     |
|---------------|----------------|------|---------------------------------|-----------------------------------------------------------------------------------------------------------------|
| 0x00          | 0x00           | 2    | **Magic**                       | Specifies whether the file is 32(`0x10B`) or 64(`0x20B`) bit.                                                   |
| 0x02          | 0x02           | 1    | **Major Linker Version**        | Major version of the linker used to create the file.                                                            |
| 0x03          | 0x03           | 1    | **Minor Linker Version**        | Minor version of the linker used to create the file.                                                            |
| 0x04          | 0x04           | 4    | **SizeOfCode**                  | Total size of all code sections when loaded in memory.                                                          |
| 0x08          | 0x08           | 4    | **SizeOfInitializedData**       | Total size of all initialized data sections when loaded in memory.                                              |
| 0x0C          | 0x0C           | 4    | **SizeOfUninitializedData**     | Total size of all uninitialized data sections when loaded in memory.                                            |
| 0x10          | 0x10           | 4    | **AddressOfEntryPoint**         | RVA (Relative Virtual Address) of the entry point function                                                      |
| 0x14          | 0x14           | 4    | **BaseOfCode**                  | RVA of the start of the code section.                                                                           |
| 0x18          | N/A            | 4    | **BaseOfData**                  | (PE32 only) RVA of the start of the data section.                                                               |
| 0x1C          | 0x18           | 4/8  | **ImageBase**                   | Preferred address of the image when loaded in memory.                                                           |
| 0x20          | 0x20           | 4    | **SectionAlignment**            | Alignment of sections when loaded in memory.                                                                    |
| 0x24          | 0x24           | 4    | **FileAlignment**               | Alignment of sections in the file on disk.                                                                      |
| 0x28          | 0x28           | 2    | **MajorOperatingSystemVersion** | Major version of the minimum OS required to run the file.                                                       |
| 0x2A          | 0x2A           | 2    | **MinorOperatingSystemVersion** | Minor version of the minimum OS required to run the file.                                                       |
| 0x2C          | 0x2C           | 2    | **MajorImageVersion**           | Major version number of the image.                                                                              |
| 0x2E          | 0x2E           | 2    | **MinorImageVersion**           | Minor version number of the image.                                                                              |
| 0x30          | 0x30           | 2    | **MajorSubsystemVersion**       | Major version of the subsystem required to run the file.                                                        |
| 0x32          | 0x32           | 2    | **MinorSubsystemVersion**       | Minor version of the subsystem required to run the file.                                                        |
| 0x34          | 0x34           | 4    | **Win32VersionValue**           | Reserved, usually set to 0.                                                                                     |
| 0x38          | 0x38           | 4    | **SizeOfImage**                 | Total size of the image, including all headers and sections, aligned to **SectionAlignment**.                   |
| 0x3C          | 0x3C           | 4    | **SizeOfHeaders**               | Combined size of the DOS Header, PE Header, Optional Header, and Section Headers, aligned to **FileAlignment**. |
| 0x40          | 0x40           | 4    | **CheckSum**                    | Checksum of the image. Required for drivers; optional for user-mode executables.                                |
| 0x44          | 0x44           | 2    | **Subsystem**                   | Specifies the subsystem required                                                                                |
| 0x46          | 0x46           | 2    | **DllCharacteristics**          | Flags indicating characteristics of the DLL                                                                     |
| 0x48          | 0x48           | 4/8  | **SizeOfStackReserve**          | Size of the stack to reserve.                                                                                   |
| 0x4C          | 0x50           | 4/8  | **SizeOfStackCommit**           | Size of the stack to commit initially.                                                                          |
| 0x50          | 0x58           | 4/8  | **SizeOfHeapReserve**           | Size of the heap to reserve.                                                                                    |
| 0x54          | 0x60           | 4/8  | **SizeOfHeapCommit**            | Size of the heap to commit initially.                                                                           |
| 0x58          | 0x68           | 4    | **LoaderFlags**                 | Reserved, usually set to 0.                                                                                     |
| 0x5C          | 0x6C           | 4    | **NumberOfRvaAndSizes**         | Number of data directories in the Optional Header.                                                              |


1. **Magic**
   - **Offset**: 0x00 (PE32 and PE32+)
   - **Size**: 2 bytes
   - **Description**: 
       - Identifies the file format: 
         - **0x10B** for **32 bit**.
         - **0x20B** for **64 bit**.
   - Used to determine between 32-bit and 64-bit executable formats.

![Magic](../.github/winpe/img_28.png)

2. **Major Linker Version**
   - **Offset**: 0x02
   - **Size**: 1 byte
   - **Description**: 
       - The major version number of the linker that generated the file.
         - Indicates the compatibility of the file with the linker software.
   - **Possible values**:
       - **2** (`0x02`)
           - **Linker Version**: Microsoft Linker 2.x
           - **Visual Studio Version**: Early Microsoft C Compilers
       - **3** (`0x03`)
           - **Linker Version**: Microsoft Linker 3.x
           - **Visual Studio Version**: Early Microsoft C/C++ Compilers
       - **5** (`0x05`)
           - **Linker Version**: Microsoft Linker 5.x
           - **Visual Studio Version**: Visual Studio 97
       - **6** (`0x06`)
           - **Linker Version**: Microsoft Linker 6.x
           - **Visual Studio Version**: Visual Studio 6.0
       - **7** (`0x07`)
           - **Linker Version**: Microsoft Linker 7.0
           - **Visual Studio Version**: Visual Studio .NET 2002
       - **7** (`0x07`)
           - **Linker Version**: Microsoft Linker 7.10
           - **Visual Studio Version**: Visual Studio .NET 2003
       - **8** (`0x08`)
           - **Linker Version**: Microsoft Linker 8.0
           - **Visual Studio Version**: Visual Studio 2005
       - **9** (`0x09`)
           - **Linker Version**: Microsoft Linker 9.0
           - **Visual Studio Version**: Visual Studio 2008
       - **10** (`0x0A`)
           - **Linker Version**: Microsoft Linker 10.0
           - **Visual Studio Version**: Visual Studio 2010
       - **11** (`0x0B`)
           - **Linker Version**: Microsoft Linker 11.0
           - **Visual Studio Version**: Visual Studio 2012
       - **12** (`0x0C`)
           - **Linker Version**: Microsoft Linker 12.0
           - **Visual Studio Version**: Visual Studio 2013
       - **14** (`0x0E`)
           - **Linker Version**: Microsoft Linker 14.0
           - **Visual Studio Version**: Visual Studio 2015
       - **14** (`0x0E`)
           - **Linker Version**: Microsoft Linker 14.1
           - **Visual Studio Version**: Visual Studio 2017
       - **14** (`0x0E`)
           - **Linker Version**: Microsoft Linker 14.2
           - **Visual Studio Version**: Visual Studio 2019
       - **14** (`0x0E`)
           - **Linker Version**: Microsoft Linker 14.3
           - **Visual Studio Version**: Visual Studio 2022
   - **NOTE**: There are probably more that are not listed

![Major Linker Version](../.github/winpe/img_27.png)

3. **Minor Linker Version**
   - **Offset**: 0x03
   - **Size**: 1 byte
   - **Description**: 
       - The minor version number of the linker that generated the file.
   - **Possible values**:
       - **0**: 
         - Initial versions; often seen in older files.
       - **10** (`0x0A`):
         - Microsoft Linker version 5.10, corresponding to Visual Studio 97.
       - **12** (`0x0C`):
         - Microsoft Linker version 6.0, corresponding to Visual Studio 6.0.
       - **16** (`0x10`):
         - Microsoft Linker version 7.0, corresponding to Visual Studio .NET 2002.
       - **20** (`0x14`):
         - Microsoft Linker version 7.10, corresponding to Visual Studio .NET 2003.
       - **30** (`0x1E`):
         - Microsoft Linker version 8.0, corresponding to Visual Studio 2005.
       - **36** (`0x24`):
         - Microsoft Linker version 9.0, corresponding to Visual Studio 2008.
       - **40** (`0x28`):
         - Microsoft Linker version 10.0, corresponding to Visual Studio 2010.
       - **46** (`0x2E`):
         - Microsoft Linker version 11.0, corresponding to Visual Studio 2012.
       - **48** (`0x30`):
         - Microsoft Linker version 12.0, corresponding to Visual Studio 2013.
       - **50** (`0x32`):
         - Microsoft Linker version 14.0, corresponding to Visual Studio 2015.
       - **52** (`0x34`):
         - Microsoft Linker version 14.1, corresponding to Visual Studio 2017.
       - **28** (`0x1C`):
         - Microsoft Linker version 14.2, corresponding to Visual Studio 2019.
       - **36** (`0x24`):
        - Microsoft Linker version 14.3, corresponding to Visual Studio 2022.
   - **NOTE**: There are probably more that are not listed

![Minor Linker Version](../.github/winpe/img_26.png)

4. **SizeOfCode**
   - **Offset**: 0x04
   - **Size**: 4 bytes
   - **Description**: 
       - The total size of all sections that contain executable code.

![SizeOfCode](../.github/winpe/img_25.png)

5. **SizeOfInitializedData**
   - **Offset**: 0x08
   - **Size**: 4 bytes
   - **Description**: 
       - The total size of all sections containing initialized data.

![SizeOfInitializedData](../.github/winpe/img_24.png)

6. **SizeOfUninitializedData**
   - **Offset**: 0x0C
   - **Size**: 4 bytes
   - **Description**: 
       - The total size of all sections containing uninitialized data.

![SizeOfUninitializedData](../.github/winpe/img_23.png)

7. **AddressOfEntryPoint**
   - **Offset**: 0x10
   - **Size**: 4 bytes
   - **Description**: 
       - RVA of the entry point function.
       - This is where execution starts after the program is loaded.

![AddressOfEntryPoint](../.github/winpe/img_22.png)

8. **BaseOfCode**
   - **Offset**: 0x14
   - **Size**: 4 bytes
   - **Description**: 
       - The RVA of the start of the code section.
       - Indicates where the code segment begins in memory.

![BaseOfCode](../.github/winpe/img_21.png)

9. **BaseOfData** (PE32 only)
   - **Offset**: 0x18
   - **Size**: 4 bytes
   - **Description**: 
       - The RVA of the start of the data section.
         - This field is not present in **PE32+**.
         - Possible for `BaseOfData` to be missing in **PE32+** (which is why there is no image of it in this course)

10. **ImageBase**
    - **Offset**: 0x1C (PE32), 0x18 (PE32+)
    - **Size**: 4 bytes (PE32), 8 bytes (PE32+)
    - **Description**: 
        - The preferred memory address at which the image should be loaded.
        - Defaults to `0x400000` for **PE32** and `0x140000000` for **PE32+**.

![ImageBase](../.github/winpe/img_20.png)

11. **SectionAlignment**
    - **Offset**: 0x20
    - **Size**: 4 bytes
    - **Description**: 
        - The alignment of sections in memory.
        - Typically `0x1000` (4KB) but must be greater than or equal to **FileAlignment**.

![SectionAlignment](../.github/winpe/img_19.png)

12. **FileAlignment**
    - **Offset**: 0x24
    - **Size**: 4 bytes
    - **Description**: 
        - The alignment of sections in the file on disk.
        - Usually set to `0x200` (512 bytes) but can vary based on the file format.

![FileAlignment](../.github/winpe/img_18.png)

13. **MajorOperatingSystemVersion**
    - **Offset**: 0x28
    - **Size**: 2 bytes
    - **Description**: 
        - The major version of the minimum required operating system.

![MajorOperatingSystemVersion](../.github/winpe/img_17.png)

14. **MinorOperatingSystemVersion**
    - **Offset**: 0x2A
    - **Size**: 2 bytes
    - **Description**: 
        - The minor version of the minimum required operating system.

![MinorOperatingSystemVersion](../.github/winpe/img_16.png)

15. **MajorImageVersion**
    - **Offset**: 0x2C
    - **Size**: 2 bytes
    - **Description**: 
        - The major version number of the image, set by the developer.

![MajorImageVersion](../.github/winpe/img_15.png)

16. **MinorImageVersion**
    - **Offset**: 0x2E
    - **Size**: 2 bytes
    - **Description**: 
        - The minor version number of the image.

![MinorImageVersion](../.github/winpe/img_14.png)

17. **MajorSubsystemVersion**
    - **Offset**: 0x30
    - **Size**: 2 bytes
    - **Description**: 
        - The major version of the subsystem that the image requires.
  
![MajorSubsystemVersion](../.github/winpe/img_13.png)

18. **MinorSubsystemVersion**
    - **Offset**: 0x32
    - **Size**: 2 bytes
    - **Description**: 
        - The minor version of the subsystem.

![MinorSubsystemVersion](../.github/winpe/img_12.png)

19. **Win32VersionValue**
    - **Offset**: 0x34
    - **Size**: 4 bytes
    - **Description**: 
        - Reserved, usually set to 0.

![Win32VersionValue](../.github/winpe/img_11.png)

20. **SizeOfImage**
    - **Offset**: 0x38
    - **Size**: 4 bytes
    - **Description**: 
        - The total size of the image, aligned to **SectionAlignment**.
        - This includes headers and all sections.

![SizeOfImag](../.github/winpe/img_10.png)

21. **SizeOfHeaders**
    - **Offset**: 0x3C
    - **Size**: 4 bytes
    - **Description**: 
        - The size of all headers combined.
        - Aligned to **FileAlignment**.

![SizeOfHeaders](../.github/winpe/img_9.png)

22. **CheckSum**
    - **Offset**: 0x40
    - **Size**: 4 bytes
    - **Description**: 
        - The checksum of the image.
        - Required for kernel-mode drivers; optional for user-mode executables.

![CheckSum](../.github/winpe/img_8.png)

23. **Subsystem**
    - **Offset**: 0x44
    - **Size**: 2 bytes
    - **Description**: 
        - Specifies the subsystem required to run the image.
    - Common values: 
        - **0x02**: Windows GUI.
        - **0x03**: Windows Console.

![Subsystem](../.github/winpe/img_7.png)

24. **DllCharacteristics**
    - **Offset**: 0x46
    - **Size**: 2 bytes
    - **Description**: 
        - Flags indicating specific characteristics of the DLL.

![DllCharacteristics](../.github/winpe/img_6.png)

25. **SizeOfStackReserve**
    - **Offset**: 0x48 (PE32), 0x48 (PE32+)
    - **Size**: 4 bytes (PE32), 8 bytes (PE32+)
    - **Description**: 
        - The size of memory reserved for the stack.
        - Defaults to 1 MB for **PE32** and 4 MB for **PE32+**.

![SizeOfStackReserve](../.github/winpe/img_5.png)

26. **SizeOfStackCommit**
    - **Offset**: 0x4C (PE32), 0x50 (PE32+)
    - **Size**: 4 bytes (PE32), 8 bytes (PE32+)
    - **Description**: 
        - The size of memory initially committed for the stack.

![SizeOfStackCommit](../.github/winpe/img_4.png)

27. **SizeOfHeapReserve**
    - **Offset**: 0x50 (PE32), 0x58 (PE32+)
    - **Size**: 4 bytes (PE32), 8 bytes (PE32+)
    - **Description**: 
        - The size of memory reserved for the heap.

![SizeOfHeapReserve](../.github/winpe/img_3.png)

28. **SizeOfHeapCommit**
    - **Offset**: 0x54 (PE32), 0x60 (PE32+)
    - **Size**: 4 bytes (PE32), 8 bytes (PE32+)
    - **Description**: 
        - The size of memory initially committed for the heap.

![SizeOfHeapCommit](../.github/winpe/img_2.png)

29. **LoaderFlags**
    - **Offset**: 0x58 (PE32), 0x68 (PE32+)
    - **Size**: 4 bytes
    - **Description**: 
        - Reserved for system use, usually set to 0.

![LoaderFlags](../.github/winpe/img_1.png)

30. **NumberOfRvaAndSizes**
    - **Offset**: 0x5C (PE32), 0x6C (PE32+)
    - **Size**: 4 bytes
    - **Description**: 
        - The number of data directories following this field.
        - Typically set to **16**, covering standard PE data directories like Import Table, Export Table, etc.

![NumberOfRvaAndSizes](../.github/winpe/img.png)

---

## Data Directory

The data directory is a set of pointers that are part of the `OptionalHeader`. It directs the Windows loader to various tables and structures that manage the execution of the program.

| Offset (PE32) | Offset (PE32+) | Size | Field Name            | Description                                            |
|---------------|----------------|------|-----------------------|--------------------------------------------------------|
| 0x60          | 0x70           | 8    | Export Table          | Exports functions and symbols for other modules.       |
| 0x68          | 0x78           | 8    | Import Table          | Imports functions and symbols from other modules.      |
| 0x70          | 0x80           | 8    | Resource Table        | Contains resources like icons, strings, and dialogs.   |
| 0x78          | 0x88           | 8    | Exception Table       | Exception handling information.                        |
| 0x80          | 0x90           | 8    | Certificate Table     | Digital signatures for verifying the file's integrity. |
| 0x88          | 0x98           | 8    | Base Relocation Table | Base address relocation information.                   |
| 0x90          | 0xA0           | 8    | Debug Directory       | Debugging information used by debuggers.               |
| 0x98          | 0xA8           | 8    | Architecture          | Reserved, generally set to 0.                          |
| 0xA0          | 0xB0           | 8    | GlobalPtr             | Reserved for global pointer information.               |
| 0xA8          | 0xB8           | 8    | TLS Table             | Thread Local Storage (TLS) initialization data.        |
| 0xB0          | 0xC0           | 8    | Load Config Table     | Security and other load configuration information.     |
| 0xB8          | 0xC8           | 8    | Bound Import          | List of functions bound to specific addresses.         |
| 0xC0          | 0xD0           | 8    | Import Address Table  | Address table for imported functions.                  |
| 0xC8          | 0xD8           | 8    | Delay Import          | Delayed loading information for imported functions.    |
| 0xD0          | 0xE0           | 8    | CLR Runtime Header    | .NET metadata for managed code.                        |
| 0xD8          | 0xE8           | 8    | Reserved              | Reserved, typically set to 0.                          |

Let’s break down each directory in detail:

1. **Export Table**
   - **Offset (PE32)**: `0x60`, **Offset (PE32+)**: `0x70`
   - **Purpose**: 
       - Contains addresses of functions, variables, and symbols that the module exports for use by other modules.
       - Used for linking and referencing functions provided by the module.
   - **Fields**:
       - **RVA**: Pointer to the start of the Export Table.
       - **Size**: Size of the Export Table data.

2. **Import Table**
   - **Offset (PE32)**: `0x68`, **Offset (PE32+)**: `0x78`
   - **Purpose**: 
       - Contains information about functions, symbols, and variables imported from other modules.
       - Used for dynamic linking, allowing the module to use functions provided by other DLLs.
   - **Fields**:
       - **RVA**: Pointer to the start of the Import Table.
       - **Size**: Size of the Import Table data.
   - You can see a visualization of the import table of this file here: [https://m4lc.io/course/winpe/imports](https://m4lc.io/course/winpe/imports)

3. **Resource Table**
   - **Offset (PE32)**: `0x70`, **Offset (PE32+)**: `0x80`
   - **Purpose**: 
       - Contains resources like icons, dialogs, menus, strings, bitmaps, and other user interface components.
       - Allows for localization and UI customization within the PE file.
   - **Fields**:
       - **RVA**: Pointer to the start of the Resource Table.
       - **Size**: Size of the Resource Table data.

4. **Exception Table**
   - **Offset (PE32)**: `0x78`, **Offset (PE32+)**: `0x88`
   - **Purpose**: 
       - Holds information for exception handling, particularly for x64 Structured Exception Handling (SEH).
       - Used to manage hardware exceptions and software-defined exceptions during execution.
   - **Fields**:
       - **RVA**: Pointer to the start of the Exception Table.
       - **Size**: Size of the Exception Table data.

5. **Certificate Table**
   - **Offset (PE32)**: `0x80`, **Offset (PE32+)**: `0x90`
   - **Purpose**: 
       - Contains digital certificates for the file.
       - Used for verifying the integrity and authenticity of the PE file, often part of Authenticode signature verification.
   - **Fields**:
       - **RVA**: Pointer to the start of the Certificate Table (points to a file offset, not an RVA).
       - **Size**: Size of the certificate data.
   - You can see what it looks like when certificates are pulled from a file here: [https://m4lc.io/course/winpe/certs](https://m4lc.io/course/winpe/certs)

6. **Base Relocation Table**
   - **Offset (PE32)**: `0x88`, **Offset (PE32+)**: `0x98`
   - **Purpose**: 
       - Contains base relocations that enable the executable to be loaded at a different base address than specified by **ImageBase**.
       - Adjusts memory addresses when the image cannot be loaded at its preferred address.
   - **Fields**:
       - **RVA**: Pointer to the start of the Base Relocation Table.
       - **Size**: Size of the Base Relocation Table data.

7. **Debug Directory**
   - **Offset (PE32)**: `0x90`, **Offset (PE32+)**: `0xA0`
   - **Purpose**: 
       - Contains information useful for debugging tools.
       - Includes information about symbols, source files, and debugging information needed for analysis.
   - **Fields**:
       - **RVA**: Pointer to the start of the Debug Directory.
       - **Size**: Size of the Debug Directory data.

8. **Architecture**
   - **Offset (PE32)**: `0x98`, **Offset (PE32+)**: `0xA8`
   - **Purpose**: 
       - Reserved for future use, typically set to 0.
   - **Fields**:
       - **RVA**: Usually set to 0.
       - **Size**: Usually set to 0.

9. **GlobalPtr**
   - **Offset (PE32)**: `0xA0`, **Offset (PE32+)**: `0xB0`
   - **Purpose**: 
       - Reserved for global pointer optimization; generally set to 0.
   - **Fields**:
       - **RVA**: Typically 0.
       - **Size**: Typically 0.

10. **TLS Table**
    - **Offset (PE32)**: `0xA8`, **Offset (PE32+)**: `0xB8`
    - **Purpose**: 
        - Contains initialization data for **Thread Local Storage (TLS)**.
        - TLS provides a mechanism for data that is specific to individual threads.
    - **Fields**:
        - **RVA**: Pointer to the start of the TLS Table.
        - **Size**: Size of the TLS Table data.

11. **Load Config Table**
    - **Offset (PE32)**: `0xB0`, **Offset (PE32+)**: `0xC0`
    - **Purpose**: 
        - Contains security features like SafeSEH, CFG (Control Flow Guard), and other load-time configurations.
        - Used to enhance security during execution.
    - **Fields**:
        - **RVA**: Pointer to the start of the Load Config Table.
        - **Size**: Size of the Load Config Table data.

12. **Bound Import**
    - **Offset (PE32)**: `0xB8`, **Offset (PE32+)**: `0xC8`
    - **Purpose**: 
        - Contains information about functions that are bound to specific addresses.
        - Helps speed up the loading process by avoiding the need for dynamic import resolution.
    - **Fields**:
        - **RVA**: Pointer to the start of the Bound Import Table.
        - **Size**: Size of the Bound Import Table data.

13. **Import Address Table (IAT)**
    - **Offset (PE32)**: `0xC0`, **Offset (PE32+)**: `0xD0`
    - **Purpose**: 
        - Provides the actual addresses of imported functions used by the executable.
        - Used during runtime to resolve addresses for dynamically linked functions.
    - **Fields**:
        - **RVA**: Pointer to the start of the IAT.
        - **Size**: Size of the IAT data.

![Data Directory Raw](../.github/winpe/img_30.png)

![Data Directory Dump](../.github/winpe/img_29.png)

---

## Sections

A section in a PE file represents different parts of the file the contain code, data, and other resources that make the file execute. Each section is defined by a header that describes its properties, size, and memory alignment.

You can see a visualization of the sections of the file used for this course here: [https://m4lc.io/course/winpe/sections](https://m4lc.io/course/winpe/sections)

Common sections in PE files include:
- `.text`
  - Contains the executable code if the program, where the CPU executes its instructions
- `.data`
  - Stories initialized global variables and static variables. These variables have a defined value before execution
- `.bss`
  - Holds uninitialized data
- `.rdata`
  - Contains read-only constants, strings, and data that should not be modified
- `.rsrc`
  - Stores resources that the file will use
- `.edata`
  - Contains the export table
- `.idata`
  - Like `.edata` but contains the import table
- `.reloc`
  - Contains relocation information allowing the executable to be loaded at different addresses
- `.pdata`
  - Contains exception data
- `.tls`
  - Holds **Thread Local Storage** that initializes values to specific threads

Each section is 40 bytes long and contains multiple pieces to it. As you can see in the image and table below:

![Raw Section](../.github/winpe/img_31.png)

| Offset | Size (Bytes) | Field Name               | Description                                                                                         |
|--------|--------------|--------------------------|-----------------------------------------------------------------------------------------------------|
| 0x00   | 8            | **Name**                 | An ASCII string representing the section name                                                       |
| 0x08   | 4            | **VirtualSize**          | The total size of the section in memory. It might be larger than the size on disk due to alignment. |
| 0x0C   | 4            | **VirtualAddress**       | The RVA of the section in memory, relative to the image base.                                       |
| 0x10   | 4            | **SizeOfRawData**        | The size of the section data in the file, aligned to the **FileAlignment**.                         |
| 0x14   | 4            | **PointerToRawData**     | The file offset where the section's data starts.                                                    |
| 0x18   | 4            | **PointerToRelocations** | The file offset of the relocation entries for the section.                                          |
| 0x1C   | 4            | **PointerToLinenumbers** | The file offset of the line-number entries for the section.                                         |
| 0x20   | 2            | **NumberOfRelocations**  | The number of relocation entries for the section.                                                   |
| 0x22   | 2            | **NumberOfLinenumbers**  | The number of line-number entries for the section.                                                  |
| 0x24   | 4            | **Characteristics**      | Flags indicating attributes of the section.                                                         |

1. **Name**

![Section Name](../.github/winpe/img_32.png)

2. **VirtualSize**

![VirtualSize](../.github/winpe/img_33.png)

3. **VirtualAddress**

![Section Name](../.github/winpe/img_33.png)

4. **SizeOfRawData** 

![SizeOfRawData](../.github/winpe/img_35.png)

5. **PointerToRawData**

![PointerToRawData](../.github/winpe/img_36.png)

6. **PointerToRelocations**

![PointerToRelocations](../.github/winpe/img_37.png)

7. **PointerToLinenumbers** 

![PointerToLinenumbers](../.github/winpe/img_38.png)

8. **NumberOfRelocations** 

![NumberOfRelocations](../.github/winpe/img_39.png)

9. **NumberOfLinenumbers**

![NumberOfRelocations](../.github/winpe/img_40.png)

10. **Characteristics**  

![Characteristics](../.github/winpe/img_41.png)

---

# That's all there is to it!

That's really all there is to it. In this course we have broken down the PE file format and provided you with visualizations of how the format works. We appreciate you taking the time to read through this course and hope you got something out of it.

Once again:

A lot of this information can be visualized and seen done during analysis by following these links:
- [https://m4lc.io/course/winpe/sections](https://m4lc.io/course/winpe/sections)
- [https://m4lc.io/course/winpe/imports](https://m4lc.io/course/winpe/imports)
- [https://m4lc.io/course/winpe/certs](https://m4lc.io/course/winpe/certs)
- [https://m4lc.io/course/winpe/info](https://m4lc.io/course/winpe/info)
- [https://m4lc.io/course/winpe/full](https://m4lc.io/course/winpe/full)
- [https://m4lc.io/course/winpe/timestamp](https://m4lc.io/course/winpe/timestamp)

Tools used for this course:
- Malcore: [https://malcore.io](https://malcore.io)
- ImHex: [https://github.com/WerWolv/ImHex](https://github.com/WerWolv/ImHex)
- Malcat: [https://malcat.fr/](https://malcat.fr/)

#### Support the Bible

Once again, this course is offered for free by The Perkins Cybersecurity Educational Fund in collaboration with Malcore! If you found this information valuable and want to support the continued development of the Malware Bible please consider:
- Donating to the Malware Bible Fund → [Donate Here](https://donorbox.org/malware-bible-fund)
- Registering for Malcore → [Sign Up](https://m4lc.io/courses/register)
- Joining the Malcore Discord → [Join Today](https://m4lc.io/courses/discord) 

#### Become a sponsor

These courses reach thousands of cybersecurity professionals, researchers, students, and teachers worldwide who actively engage in learning and advancing the field. Sponsoring our educational initiative not only supports free cybersecurity education but also places your brand in front of a highly technical and security-conscious audience.

Interested in partnering? Let's talk about how your organization can be featured in our future courses: [Contact us today!](https://perkinsfund.org/) Please view our [Sponsorship Packages](../.github/sponsorships/sponsorship_package.md) for more details!
