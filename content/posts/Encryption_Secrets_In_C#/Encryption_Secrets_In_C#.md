---
title: "Encryption_Secrets_In_C#"
date: 2024-10-12T09:57:38+08:00
draft: false
categories:
    - "dev"
    - "c#"
---
# Table of Contents
- [Desc](#desc)
- [Code](#code)
- [Note](#note)
- [Reference](#reference)

## Desc
We do not want to put our secrets as plain text in app settings configuration file. In this situation, we can use the *symmetric key* for encrypting and decrypting data in C#.

## Code
Below is the sample class for encryption and decryption using *Symmetric Key*

    using System;
    using System.IO;
    using System.Security.Cryptography;
    using System.Text;

    namespace EncryptionDecryptionUsingSymmetricKey
    {
        public class AesOperation
        {
            public static string EncryptString(string key, string plainText)
            {
                byte[] iv = new byte[16];
                byte[] array;

                using (Aes aes = Aes.Create())
                {
                    aes.Key = Encoding.UTF8.GetBytes(key);
                    aes.IV = iv;

                    ICryptoTransform encryptor = aes.CreateEncryptor(aes.Key, aes.IV);

                    using (MemoryStream memoryStream = new MemoryStream())
                    {
                        using (CryptoStream cryptoStream = new CryptoStream((Stream)memoryStream, encryptor, CryptoStreamMode.Write))
                        {
                            using (StreamWriter streamWriter = new StreamWriter((Stream)cryptoStream))
                            {
                                streamWriter.Write(plainText);
                            }

                            array = memoryStream.ToArray();
                        }
                    }
                }

                return Convert.ToBase64String(array);
            }

            public static string DecryptString(string key, string cipherText)
            {
                byte[] iv = new byte[16];
                byte[] buffer = Convert.FromBase64String(cipherText);

                using (Aes aes = Aes.Create())
                {
                    aes.Key = Encoding.UTF8.GetBytes(key);
                    aes.IV = iv;
                    ICryptoTransform decryptor = aes.CreateDecryptor(aes.Key, aes.IV);

                    using (MemoryStream memoryStream = new MemoryStream(buffer))
                    {
                        using (CryptoStream cryptoStream = new CryptoStream((Stream)memoryStream, decryptor, CryptoStreamMode.Read))
                        {
                            using (StreamReader streamReader = new StreamReader((Stream)cryptoStream))
                            {
                                return streamReader.ReadToEnd();
                            }
                        }
                    }
                }
            }
        }
    }

Now, we can write the following code to test.

    using System;

    namespace EncryptionDecryptionUsingSymmetricKey
    {
        class Program
        {
            static void Main(string[] args)
            {
                var key = "XXXXXXXXXXXXXXXXXXXXXXXX"; #random string

                Console.WriteLine("Please enter a string for encryption");
                var str = Console.ReadLine();
                var encryptedString = AesOperation.EncryptString(key, str);
                Console.WriteLine($"encrypted string = {encryptedString}");

                var decryptedString = AesOperation.DecryptString(key, encryptedString);
                Console.WriteLine($"decrypted string = {decryptedString}");

                Console.ReadKey();
            }
        }
    }

## Note
There are other ways to protect secrets in C#, like using Azure Key Vault. However, it will need an Azure Subscription and not very convenience.

## Reference
https://www.c-sharpcorner.com/article/encryption-and-decryption-using-a-symmetric-key-in-c-sharp/