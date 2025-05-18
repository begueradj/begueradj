---
title: "Vigenère cipher simplified implementation"
date: 2017-11-12T12:33:21+08:00
categories: ["security"]
draft: false
---
This is not an other dissertation on Vigenère cipher. This encryption algorithm is profusely discussed in cryptography literature. Here is rather  a simplified yet efficient implementation of it.


The goal of this article is not to about debating the theory of Vigenère cipher. I rather just want to focus on its implementation after I have seen several awkward ways of proceeding to it on different online programming forums.

This [question](https://codereview.stackexchange.com/questions/178047/vigenere-cipher-make-code-more-concise) on Code Review website is an example of such implementations where code duplication is obvious but which the pre-existing answers did not address.


This is the usual Vigenère cipher square where the upper horizontal columns contains the key’s alphabet whereas the most left vertical column corresponds to the alphabet of the message to cipher (or deciphered):
![Vigenère cipher square]({filename}../images/vigenere_cipher.png)

Here is a text-like pseudo algorithm of the core Vigenere cipher function which can be better understood in practice by checking the code beneath it:
```cmd
- Initialize the index of the key to 0
- For each letter of the message:
   - weight = weight of the letter in the alphabet
   - if encryption then:
       - weight = weight + weight of the corresponding letter in the secret key 
   - else if decryption then:
       - weight = weight - weight of the corresponding letter in the secret key
   - weight = weight modulo 26
   - if letter is lowercase then:
       - append letter to the resulted text as it is
   - else:
       - capitalize the letter
       - append the letter to the resulted text
   - increment the index of the key
   - reset the index of the key to zero if index is equal to the length of the key
```

Here is a Python 3 implementation sample:
```python
import string


class FileOperations:
   def __init__(self):
       pass
   
   def get_file_content(self, input_file):
       with open(input_file,'r') as input_file:
           return input_file.read()
   
   def save_to_file(self, output_file, content):
       with open(output_file, 'w') as output_file:
           output_file.write('{0}'.format(content))


class Vigenere:
 
   def __init__(self):
       self.__SECRET_KEY = 'SOMELONGSECRETKEYGOESHERE'
       self.file_operations = FileOperations()
       self.__message = self.file_operations.get_file_content('plain.txt')
      
   def encrypt(self):
       encrypted_text = self.__vigenere('encryption')
       self.file_operations.save_to_file('encrypted.txt', encrypted_text)

   def decrypt(self):
       decrypted_text = self.__vigenere('decryption')
       self.file_operations.save_to_file('decrypted.txt', decrypted_text)

   def __vigenere(self, flag):
       transformed_text = []
       index = 0
       for letter in self.__message.upper():
           weight = string.ascii_uppercase.find(letter)
           if weight != -1:
               if flag == 'encryption':
                   weight += string.ascii_uppercase.find(self.__SECRET_KEY[index])
               elif flag =='decryption':
                   weight -= string.ascii_uppercase.find(self.__SECRET_KEY[index])
               weight %= len(string.ascii_uppercase)
               if letter.isupper():
                   transformed_text.append(string.ascii_uppercase[weight])
               elif letter.islower():
                   transformed_text.append(string.ascii_uppercase[weight]).lower()
               index += 1
               if index == len(self.__SECRET_KEY):
                   index = 0
           else:
               transformed_text.append(letter)
       return ''.join(transformed_text)
       

if __name__ == '__main__':
   vigenere = Vigenere()
   vigenere.encrypt()
```
