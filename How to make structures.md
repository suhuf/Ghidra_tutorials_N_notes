 Hey,

I'm writing this mostly for my own benefit to remember how to do this after learning this twice via hextree.io. I highly reccomend you check that site out for detailed tutorials from Liveoverflow and Stacksmashing.

Sometimes when reversing software we may run into something our decompiler does not get a proper analysis of, as a result we manually have to use the decompilers tools ourselves to fix the issue or just make things more readable for the human eye. 

In this case we are going to be making something more readable through using structures.

Here I am going to be using an example program from hextree as it is the most straight forward program to use.

After importing it in, we can get to the more important parts:


![image](https://github.com/user-attachments/assets/49bca6b5-af91-4e52-84ad-09b0090d7991)

First we take a look at main, clearly the program is taking an input configuration file and then giving a flag based on the contents, however the flag is not our focus. Our focus is the functions within the program that refrence a structure. Let's check out the 'parse_file' function:

![image](https://github.com/user-attachments/assets/870eab67-b454-4764-bc06-dc40fa6f58a4)

Now here we have found what we are looking for, we notice that in the fscanf parameters 'param2' is repeated multiple times with different byte offsets, this is because param2 is actually a structure, not a singular value and is holding multiple values within itself, 

How can we re-write this however to look more coherent?

Firstly, we can use Ghidra's auto-struct function, we right click on param2 and choose auto create structure:  ![image](https://github.com/user-attachments/assets/7b158544-39f8-4563-bea6-bf4a3acd919a)

Now it looks a lot different, next we right click on param2 again and select 'edit data type' where a new window should popup named 'Structure editor':

![image](https://github.com/user-attachments/assets/0d3b5d8c-9ee3-47d2-9220-0ca8730c6267)

Now we can indepently edit every value to have its own name and respective datatype, lets start with the first value which shoudl have an offset of 0.

We select 'data type'. Looking at the earlier parameter, **'s_IP=%15s_Port=%d_Username=%19s_Pa_140009058'** we can also see that it is asking firstly for a char[16] (due to **%15s**), so lets set the data type to that. We can alos set the name to IP.

![image](https://github.com/user-attachments/assets/3d0b5878-e88c-4e2b-8e46-4f90be14602e)

Now when we check the structure editor we can see that it has already occupied the respective amount of bits before our next value at offset 0x10.

So lets get started on the next value then: The Port.

![image](https://github.com/user-attachments/assets/95cfa65f-6221-4865-af10-5b4522f78c03)

Here we see that fscanf is expecting a value that represents %d with no size specified. In C, %d is a format specifier for **integers**

as a result we know that this value _Should_ be an integer. So lets set that in the structure editor.

We do the same steps as before, select data type to integer, and name the offset accordingly:

![image](https://github.com/user-attachments/assets/796cb8f7-6294-4991-ac2f-215878e2544b)

and here we find a similar thing happened to last time. Lets continue with the next value, the username.

![image](https://github.com/user-attachments/assets/95cfa65f-6221-4865-af10-5b4522f78c03)

Again we see that fscanf is expecting a parameter equal to %s except that this time the value is 19, so we can do the same format as last time just seting the size to char[20], we also can set the respective name to username:

![image](https://github.com/user-attachments/assets/05cc1520-3cb3-4765-b382-9f6ca352bbee)






















