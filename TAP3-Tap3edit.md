### What is TAP3::Tap3edit?
TAP3::Tap3edit is a Perl module. When you use it into a Perl script you are able to edit GSM TAP3 and RAP files encoded with the ASN.1 GSM specification.

What does it mean? It means with this library you can write for example a script to decode a TAP 3 or a RAP file, change its content in a way you want, and encode it again into the same or a new TAP 3 file.

### What is the advantage against the visual TAP3 editors?
Since Tap3edit is not interactive as the other Visual Windows TAP3 editor (no window or icon to click to) you can write a script or a batch file in Perl language to do all the job for you. You can create the script once, and then apply it automatically to all the files you want! You can even give it to other users.

If you are a C experienced programmer with knowledge of ASN.1, you'll know what it means to change a TAP 3 file with a simple 10 lines script.


### Who can use this tool?
Since this module is designed to be used from Perl, you need to be familiar with this language. You need to have some experience with handling of hashes under Perl and with the ASN.1 structure of TAP3 Files, which is defined in the TD.57 document from the [GSM Association](http://www.gsm.org) for TAP 3 Files and in the TD.32 for RAP files.

In other words, **TAP3::Tap3edit** is a Perl module oriented mostly to developers with some experience with Perl and TAP 3 Files.

But anyhow, if you don't have any experience either with Perl or TAP 3 Files, you may want to try it anyway. In this page I'll give you some tips and examples to make small changes to your TAP and RAP files.

### What can TAP3::Tap3edit do?
Ok. You know now what this module is and who can use it. But you are asking yourself what it can exactly do, or better said, what the possibilities are by using it.
Well, let's put it in some examples:

#### Example: Problem with the Billing System 1
Let's suppose you've changed something in your billing system and all your TAP files were created as normally. After two days you realize all the files were rejected at your clearing house! After a while, you identify and solve the configuration problem in your billing system, but you still have 400 files you need to repair and send again to your clearing house. So this is a good moment to use **TAP3::Tap3edit**.

#### Example: Problem with the Billing System 2
Let's change slightly the previous example. You had the trouble and you cannot even solve the problem in your billing system. That means you cannot stop creating TAP files with wrong information. And you need to send those calls to your roaming partners! What to do??? Well, you can create a Perl script using the **TAP3::Tap3edit** module to patch and fix all your TAP files, then you can configure it to run for every file before being sent. In that way your billing system creates TAP files with wrong information, you patch them with your script, your clearing house does not reject them anymore and your roaming partners receive all the calls.

#### Example: Statistics
For some reason you need to make some statistics and need to get the information directly from the TAP files. How to extract the information from the files? You can use **TAP3::Tap3edit**. You need to know for example all the duration of every IMSI in the last week. So collect all the files with calls in the last week. Then make a program to read all TAP files and store the IMSI and its duration in some table on some database. Afterwards you run the program on all the files. Now you just need to go to the database and run a query. These are your statistics.

### What else can you do?
In fact, you can do anything you want and the limit is your imagination. Perl is a very powerful language. To give you some other ideas you could build a program to convert a TAP3 file into [XML](https://metacpan.org/search?q=xml), or even to TAP2 or to another release of TAP3 (Note: for these cases you need to know of course all the rules for the conversion).

It is not always necessary to decode and encode. You can just decode to get only information from the file (look the example of the statistics). Or you can only encode if you already have the structure. This is useful to create TAP or RAP files, for example a notification file.

Alright, for the moment it's enough of examples, I think you've got already the idea. It's time to see how it works on the practice.


### How to do it?
With this small example you can see how a simple perl program to edit a TAP3 looks like.

#### Decode
		
```
1 #!/usr/bin/perl

2 use TAP3::Tap3edit;
3 $tap3 = TAP3::Tap3edit->new;
4 $tap3->decode("CDOPER1OPER200001") or die $tap3->error;
5 $struct=$tap3->structure;	
```

This is the first step to work with **TAP3::Tap3edit**. With these five lines we just decode the TAP file "CDOPER1OPER200001". Let's go line by line.

1. Tells the shell which kind of script this is and which program it should run. Please make sure that your perl is located under this directory. In some Unix versions is under /usr/local/bin/perl. For windows you may write the location on windows e.g. c:/perl/bin/perl.exe (please note it is /, and not \\).

2. In this line we tell perl that we are going to use the module TAP3::Tap3edit. Perl may complain here if the package is not installed.

3. We create a new instance of the object TAP3::Tap3edit. Its name is $tap3.

4. Here the package method "decode" is used to decode the file CDOPER1OPER200001.

5. The complete structure of the file is stored in the variable $struct. Here is important to notice that the file has been already decoded and its complete information (tree) is stored in this variable.

#### Structure change
After decoding we want to change something from the file. Let's change for example the UTC Offset.

		
```
1 #!/usr/bin/perl

2 use TAP3::Tap3edit;
3 $tap3 = TAP3::Tap3edit->new;
4 $tap3->decode("CDOPER1OPER200001") or die $tap3->error;
5 $struct=$tap3->structure;

6 $struct->{transferBatch}{auditControlInfo} {earliestCallTimeStamp}{utcTimeOffset} = "+0300";
```

Well if you are acquainted with the structure of a TAP file you'll see that in the line 6 we just change the "UTC Offset" of the "Earliest Call" inside the "Audit Control Information"

**Note**: Please note that in this case we changed an ASCII field. If you need to change a hexadecimal field like for example the IMSI, you need to convert the value first to hexadecimal.

#### Encode
Ok. Until now it doesn't look that complicated. So now that we changed our structure, we need to encode it.

```
1 #!/usr/bin/perl

2 use TAP3::Tap3edit;
3 $tap3 = TAP3::Tap3edit->new;
4 $tap3->decode("CDOPER1OPER200001") or die $tap3->error;
5 $struct=$tap3->structure;

6 $struct->{transferBatch}{auditControlInfo} {earliestCallTimeStamp}{utcTimeOffset} = "+0300";

7 $tap3->encode("CDOPER1OPER200001.new") or die $tap3->error;
```
		
In that 7th line we see how we encode the changed structure into a new file. So after running the script we will have the original file and a file ".new" with the changes.

More examples can be found [here](https://github.com/tap3edit/TAP3-Tap3edit/tree/master/examples)

### What do I need to install it?
Alright, since this Module is a Perl module you need of course Perl. If you are using unix or linux you have it possibly already installed in your system. Please check the version you are using. I built the Module with **Perl 5.8.6**. I did not try the module with older versions. So you may want to update to the latest version. 
#### Unix/Linux
I developed the Module on Linux Suse 9.1 and there everything worked fine. I tried it as well on Sun Solaris and worked in the same way. No complaints from the system. You can find the Perl binaries for Unix/Linux under following link:
[http://www.perl.com/CPAN/ports/index.html](http://www.perl.com/CPAN/ports/index.html)

In fact here you can find binaries of Perl for all the systems.

#### Windows
I didn't try **TAP3::Tap3edit** on a windows platform but I suppose it should work in the same way as it did on unix.

You can find Perl for windows under following link:

[http://www.activestate.com/Products/ActivePerl/?_x=1](http://www.activestate.com/Products/ActivePerl/?_x=1)

#### Macintosh
I've never worked on a Mac, so I don't know if it works or not. But since Perl is multi platform should work in the same way as it does on Unix.

Here the link:

[http://www.macperl.com/](http://www.macperl.com/)

### What else do I need?
**TAP3::Tap3Edit** uses another module to handle the ASN.1 structures. The Module is called **Convert::ASN1** and it was written by Graham Barr. In the latest versions of Linux this module comes included in the installation package (At least I saw it on Suse). This module should be installed as any other Perl Module, you just need to follow the instructions (In fact is the same way to install **TAP3::Tap3edit**).
From here you can download the latest version:

[http://search.cpan.org/search?query=Convert-ASN1&mode=all](http://search.cpan.org/search?query=Convert-ASN1&mode=all)

### How to install TAP3::Tap3edit?

- Automatically (Preferred method)

To install automatically (needs connection to the internet) use the command **cpan**.
Once inside its command line type following instruction:

  ```
  cpan[1]> install TAP3::Tap3edit
  ```
**Note**: This will install automatically all the dependencies too.

- Manually

First the module has to be downloaded locally from https://metacpan.org/search?q=tap3edit
To install manually run these commands, substituting x.xx with the version number that you have downloaded.

  ```
  gunzip TAP3-Tap3edit-x.xx.tar.gz
  tar xvf TAP3-Tap3edit-x.xx.tar
  cd TAP3-Tap3edit-x.xx
  perl Makefile.PL
  make
  make test
  make install
  ```
**Note**: You will need to install all dependencies manually too.
 
