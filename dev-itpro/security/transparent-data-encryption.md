---
title: "Data Encryption at Rest, Transparent Data Encryption (TDE) and BitLocker"
ms.custom: na
ms.date: 28/02/2019
ms.reviewer: na
ms.suite: na
ms.tgt_pltfrm: na
ms.topic: article
ms.service: "dynamics365-business-central"
ms.author: kepontop
---

# Data Encryption at Rest
When thinking about data encryption, you should mainly consider three scenarios:

- Encrypting your data at rest, which means encrypting it while it is stored on whatever file storage you use.
- Encrypting your data in transit, which means encrypting data while it travels through private or public network communication channels.
- Encrypting your data in use, which means encrypting it while it is actively used in RAM or CPU caches and registers.

This article will show you the alternatives for encrypting data at rest. In a scenario where the physical media (such as drives or backup tapes) or the database files are stolen, a malicious party can just restore or attach the database and browse the data, if you don't encrypt it. The following mechanisms give you different possibilities to help prevent this.

> [!IMPORTANT]
> Data encryption at rest does not provide encryption across communication channels. For more information about how to encrypt data across the communication channel between the database and the [!INCLUDE[server](../developer/includes/server.md)], see [Enhancing Business Server Security](enhancing-server-instance-security.md). It also doesn't provide encryption while the data is in use. SQL Server offers the [Always Encrypted](https://docs.microsoft.com/de-de/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sqlallproducts-allversions) feature for this, but it is not supported for [!INCLUDE[prodshort](../developer/includes/prodshort.md).

## Transparent Data Encryption (TDE)

You can use Transparent Data Encryption (TDE) to encrypt SQL Server and Azure SQL Database data files at rest. With TDE you can encrypt the sensitive data in the database and protect the keys that are used to encrypt the data with a certificate. TDE performs real-time I/O encryption and decryption of the data and log files to protect data at rest. TDE can assist in the ability to comply with many laws, regulations, and guidelines established in various industries. If a malicious party would be able to steal your data files, they still would not be able to use them at all because the would need the keys as well.

> [!CAUTION]
> Backup files of databases that have TDE enabled are also encrypted by using the database encryption key. As a result, when you restore these backups, the certificate protecting the database encryption key must be available. This means that in addition to backing up the database, you must make sure that you also maintain backups of the server certificates to prevent data loss. Data loss will result if the certificate is no longer available.

For more information about TDE, see [Transparent Data Encryption (TDE)](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption).

## BitLocker Drive Encryption

BitLocker is a volume encryption feature included in Windows Server. It protects at the file volume level so when the database server is online, the volume is unlocked, though not decrypted. If a malicious party would be able to steal your whole drive, they still would not be able to attach and use it.

> [!CAUTION]
> Be aware that detaching or backing up a database to a different volume that is not protected by BitLocker causes any protection the file currently has to be lost.


For more information about BitLocker, see [the overview](https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-overview) and how it applies to [Windows Server 2012 and later](https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server) 

## Defense in depth with BitLocker Drive Encryption and Transparent Data Encryption (TDE) combined

Bitlocker encrypts the drive and does so transparently. The effect is that if the drive gets stolen, nobody can use the data. But if someone gained access to the drive via Windows, Network Share etc., he could copy the file and on the new drive the file is not encrypted any more. TDE on the other hand encrypts the database file itself (and also backup). So if either file is copied/stolen, the attacker cannot read it. But if the attacker gains access to the whole drive including SQL Server, he can start SQL Server and read the data from there. So both technologies complement each other, and it is recommended that you use BitLocker together with TDE for defense in depth.

## Performance impact

TDE has an estimated performance impact around 3-5% and can be much lower if most of the data accessed is stored in memory. The impact will mainly be on the CPU, I/O will have a smaller impact. See the [SQL documentation on this topic](https://docs.microsoft.com/en-us/previous-versions/sql/sql-server-2008/cc278098(v=sql.100)#_Toc189384687) for more details.

BitLocker Drive Encryption has a single-digit percentage performance impact as explained [here](https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-deployment-and-administration-faq#is-there-a-noticeable-performance-impact-when-bitlocker-is-enabled-on-a-computer).

## See Also  
 [Classifying Data](../developer/devenv-classifying-data.md)   
 [Data Security](data-security.md)  
 [Enhancing Business Server Security](enhancing-server-instance-security.md)   
 [Deploy a Business Central Database to Azure SQL Database](../deployment/deploy-database-azure-sql-database.md)  
[Security and Protection](security-and-protection.md)  