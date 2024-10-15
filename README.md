# NFC Decryption and Cracking Project Documentation

## Table of Contents

1. [Project Overview](#project-overview)
2. [Objectives](#objectives)
3. [Tools and Equipment](#tools-and-equipment)
4. [Methodology](#methodology)
   - [Initial Setup](#initial-setup)
   - [Data Extraction](#data-extraction)
   - [Key Extraction and Dictionary Attack](#key-extraction-and-dictionary-attack)
   - [Data Analysis and Value Location](#data-analysis-and-value-location)
   - [Data Modification](#data-modification)
5. [Writing Modified Data](#writing-modified-data)
6. [Final Results](#final-results)
7. [Conclusion](#conclusion)
8. [References](#references)

## Project Overview

This project focuses on the analysis, modification, and manipulation of data stored on NFC (Near Field Communication) cards, specifically MIFARE Classic 1K cards. The project involved the extraction of data encoded in hexadecimal format, identifying specific numeric values, modifying them as needed, and exploring the security implications of NFC technology. The goal was to gain a comprehensive understanding of the data structure of NFC cards and develop skills in data manipulation, focusing on the practical applications of these concepts in various scenarios.

## Objectives

The primary objectives of this project were:

- **Understand NFC Card Data Structure**: Gain a thorough understanding of how data is stored and organized on MIFARE Classic cards.
- **Extract and Modify Values**: Learn to extract stored values, identify their encoding, and modify them to achieve specific outcomes.
- **Utilize Tools for NFC Operations**: Familiarize myself with various tools and platforms used for reading, writing, and cloning NFC card data.
- **Explore Security Implications**: Investigate the security features, vulnerabilities, and potential for unauthorized access associated with NFC card technology.

## Tools and Equipment

The following tools and equipment were used throughout the project:

- **NFC Reader**: PN532 NFC module
- **USB-to-TTL Adapter**: To establish a reliable serial communication link between devices, allowing for debugging and data exchange.
- **Raspberry Pi**: Used for running Raspbian OS and the **libnfc** library, which facilitated NFC operations on a Linux platform.
- **Smartphone**: Samsung Galaxy S23 Ultra utilized for testing NFC interactions and using the MIFARE Classic Tool app for data manipulation.
- **Software**: 
  - **libnfc**: An open-source library that allows applications to communicate with NFC devices.
  - **MIFARE Classic Tool**: A mobile application for Android that enables reading and writing NFC data, specifically for MIFARE cards.

## Methodology

### Initial Setup

1. **USB-to-TTL Adapter Verification**:
   - The first step involved connecting the PN532 NFC reader to a Windows PC using a USB-to-TTL adapter. I verified the functionality of the NFC reader by testing communication through a serial terminal, ensuring that the RX (receive) and TX (transmit) pins were correctly wired. This setup was crucial for ensuring that data could be exchanged effectively between the NFC reader and the computer.

2. **Raspberry Pi Configuration**:
   - After confirming the successful communication on Windows, I flashed **Raspbian** onto my Raspberry Pi. This was essential to utilize the **libnfc** library, which offers better compatibility for NFC operations on Linux. The Raspberry Pi setup included installing necessary drivers and libraries to facilitate NFC interactions.

### Data Extraction

1. **NFC Card Identification**:
   - For this project, I worked with a **MIFARE Classic 1K** card, which is commonly used in access control systems and public transportation. I initiated the extraction of data structured across 16 sectors of the card, focusing on specific locations where relevant numeric values were stored.

2. **Extracted Data**:
   - The hexadecimal representation of the extracted data from the NFC card was documented as follows:

   ```
   +Sector: 0
   31F18DCE8308040003444235A64B6A90
   3030000100000172337400433411EEBD
   01010DB66C190000005F050100000095
   0734BFB93DAB7877880085A438F72A8A
   +Sector: 1
   DB01190024FEE6FFDB01190004FB04FB
   00000000000000000000000000000000
   00000000000000000000000000000000
   0734BFB93DAB6877890085A438F72A8A
   +Sector: 2
   DB01190024FEE6FFDB01190004FB04FB
   BB410F0044BEF0FFBB410F0009F609F6
   303000010000017233744E4554110000
   0734BFB93DAB48778B0085A438F72A8A
   +Sector: 3
   00433402FFBCCBFD0000000000000000
   51433730313438383302020000000000
   00000000000000000000000000000000
   0734BFB93DAB7F07880085A438F72A8A
   ```

   The data was organized by sector, and specific attention was paid to the sectors where monetary values were likely stored.

### Key Extraction and Dictionary Attack

1. **Using libnfc**:
   - To retrieve the necessary encryption keys for accessing the data on the MIFARE Classic card, I employed a dictionary attack technique utilizing **libnfc**. This method involved systematically testing various key combinations against the card to uncover the two unique keys (A and B) required for the first four sectors.

2. **Key Retrieval**:
   - The dictionary attack successfully extracted the following keys:
     - Key A: **FC00018778F7**
     - Key B: **5C598C9C58B5** for the remaining sectors, typically using the default **FFFFFFFFFFFF** key for sectors 4-15. This retrieval process highlighted the vulnerabilities in the security of MIFARE Classic cards, especially when weak keys are employed.

### Data Analysis and Value Location

1. **Data Structure Analysis**:
   - After retrieving the keys, I proceeded to analyze the extracted data thoroughly, focusing on identifying the specific locations where monetary values were encoded. Upon inspection, I discovered that the card stored a value of **$4.75**, represented in hexadecimal as **`DB 01`** located in **Sector 1** and **Sector 2**.

2. **Hexadecimal Representation**:
   - The structured format of the extracted data allowed me to trace the hexadecimal representations of values stored across different sectors. I documented the layout of the sectors, identifying that the values were consistently located in specific byte positions.

### Data Modification

1. **Target Value Change**:
   - With the original value of **$4.75** in mind, I set out to modify it to **$20.75**. This conversion process involved several key steps:
     - Convert the new value **$20.75** to decimal, yielding **2075**.
     - Convert **2075** to hexadecimal, which results in **081B**.
     - Reverse the byte order for little-endian representation, resulting in **1B 08**.

2. **Data Replacement**:
   - The next step was to replace the original value in the card data. I documented the data modifications made:

   **Original Data (Sector 1 & Sector 2)**:
   ```
   DB 01 19 00 24 FE E6 FF ...
   ```

   **Modified Data (Sector 1 & Sector 2)**:
   ```
   1B 08 19 00 24 FE E6 FF ...
   ```

### Writing Modified Data

1. **Using MIFARE Classic Tool App**:
   - To write the modified data back to the NFC card, I used the **MIFARE Classic Tool** app on my Samsung Galaxy S23 Ultra. This app allowed me to enter the extracted keys and access the sectors for writing operations. After entering the necessary keys into the app’s key file, I initiated the writing process to update the card data.

2. **Final Modified Data**:
   - The final data written to the NFC card reflected the new value of **$20.75**. I compiled the updated NFC card data to ensure accuracy and completeness, as follows:

   ```
   +Sector: 0
   31F18DCE8308040003444235A64B6A90
   3030000100000172337400433411EEBD
   01010DB66C190000005F050100000095
   0734BFB93DAB7877880085A438F72A8A
   +Sector: 1
   1B08190024FEE6FF1B08190004FB04FB
   00000000000000000000000000000000
   00000000000000000000000000000000
   0734BFB93DAB6877890085A438F72A8A
   +Sector: 2
   1B08190024FEE6FF1B08190004FB04FB
   BB410F0044BEF0FFBB410F0009F609F6
   303000010000017233744E4554110000
   0734BFB93DAB48778B0085A438F72A8A
   +Sector: 3
   00433402FFBCCBFD0000000000000000
   51433730313438383302020000000000
   00000000000000000000000000000000
   0734BFB93DAB7F07880085A438F72A8A
   ```

## Final Results

Upon completing the project, I successfully extracted, modified, and wrote new data to a MIFARE Classic 1K card. The final card data reflected the modified monetary value of **$20.75**, demonstrating the effectiveness of the techniques used in data extraction and modification.

## Conclusion

This project not only provided a practical application of NFC technology but also highlighted the security vulnerabilities associated with MIFARE Classic cards. Through detailed documentation of the methodologies employed, it became evident that the security mechanisms in place can be circumvented if weak keys are used. The skills gained during this project will be invaluable for my future endeavors in the realm of technology, cryptography, and data manipulation.

## References

- [PN532 NFC Reader Documentation](https://www.elechouse.com/elechouse/images/product/PN532_module_V3/PN532_%20Manual_V3.pdf)
- [libnfc Project Documentation](http://www.libnfc.org/api/index.html)
- [MIFARE Classic Tool App Documentation](https://github.com/ikarus23/MifareClassicTool)
- [NFC Forum Specifications](https://nfc-forum.org/build/specifications)
- [MIFARE Classic Overview](https://www.mifare.net/en/mifare-classic/)
- [Dictionary Attack Methodologies](https://en.wikipedia.org/wiki/Dictionary_attack)

