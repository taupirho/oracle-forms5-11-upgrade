**A case study in upgrading an Oracle Forms 5 application to Oracle Forms 11g**

Tom Reid

**For as long as I care to remember the demise of Oracle Forms as a tool for
developing GUI interfaces to the database has long been predicted. I’m happy to
say that, in those famous words, the reports of its death have been greatly
exaggerated. I thought I'd share my recent experience in upgrading our Oracle
Forms 5 application to Oracle Forms 11g.**

First a bit of background. Historically, Oracle Forms applications were the main
way for non-technical users to interact with the Oracle database. They presented
a GUI interface that enabled users to select, insert and update data without
having to know any SQL or underlying table structures at all. In our own case
before Forms 5, our Forms 4 application was, frankly, a bit rubbish and we did a
wholesale update to Forms 5 around 2001 and since that time it’s been the
primary interface to our Oracle database in use by our data analysts. It served
us well in all database versions we ran from Oracle 7 through Oracle 9i. However
when we upgraded our database to Oracle 10g we hit a snag. I know what you’re
probably thinking, “… those guys are still just on Oracle 10!!” In our defence
our main server is an OpenVMS Compaq Alpha and let us be kind and say that in
terms of database releases and for various reasons the OpenVMS platform hasn’t
always been a priority for Oracle.

Although our Forms 5 application could still run against our 10g database, we
found we couldn't develop new Forms against it. Compiling any changed Forms
whilst connected to it resulted in the Form builder application crashing. The
stop-gap solution was of course to install a separate Oracle 9i database and
develop against that but deploy against our Oracle 10 database. This wasn't
ideal though as we had to basically replicate a great deal of the Oracle 10
database table structures and contents to the Oracle 9 database just to get the
form to compile and in any case who knows what would have happened when we
upgraded to Oracle 11 or 12. So, the decision was taken to upgrade to a more
modern version and we chose Forms 11g for no better reason other than we had
just happened to receive a shiny new 11g install disk from Oracle. What follows
is pretty much a step-by-step process of what we did next.

The first thing you have to get your head around is that Forms 11g runs under a
totally different architecture than Forms 5 did. The Forms 11g run-time is a
web-based 3-tier type architecture compared with Forms 5 client/server
architecture. What this means is that you will be accessing your Forms 11 forms
via a web browser and importantly some of the things you did in Forms 5 either
don’t work in the same way under Forms 11 or just don’t work full stop.

Believe me when I say that the most difficult step you'll face in upgrading a
Forms 5 app to Forms 11g will probably be the installation of the new Forms 11g
binaries itself. There are two main parts to the installation. The webserver -
which is easy and the fusion/Forms11g bit itself - which ironically is also easy
as long as your system is correctly set-up beforehand to begin with!  I nearly
tore my hair out over this step as I just couldn't get the new Forms installed
properly at all. The reason it turned out was simply due to the ports in our
server not being fully opened due to IT security concerns. Once we had arranged
with our IT department to provide a server with the ports open, the installation
was a cinch.  My advice is this. If you have taken care of the really obvious
and stupid requirements such as no spaces in environment variables like PATH and
TMP and so on and you are still having issues installing Forms there is really
something more fundamental going on such as the issue we had with ports or virus
checking software interfering with your install. You need to get this sorted
first before doing anything else.

At this stage you will also have to decide which of the two main Forms
installation modes you will use, deployment or development. My advice is to go
for deployment. Don’t worry, deployment doesn’t mean you can’t develop Forms but
it does hold some advantages over the development mode such as the installation
of  the Enterprise Manager tool which can be very useful indeed.

The next step is to install Oracle Forms and Reports version 6. Come again? I
thought this was a Forms 5 to Forms 11g upgrade. Why are we installing Forms 6
too? Well, in case you didn’t know already the bad news is that the upgrade path
from Oracle 5 forms to Oracle 11g forms **requires** that you upgrade first of
all to Oracle Forms 6 as an intermediate step. Don’t worry, this is not as
onerous as you might think and generally only involves opening the Forms 5 form
using the Forms 6 builder tool, re-compiling it and saving it. If you don't have
a copy of Oracle Forms 6 lying around you need to contact Oracle support. They
will give you a link – normally valid for 24 hours - where you can still
download this version.

At this point you should also take this opportunity to identify exactly what
Forms you actually need to convert. In our case, out of approximately 340 Forms,
we chose to convert some 200 of them. So use this time to get rid of the Forms
you don’t use. We used a simple traffic light system. Green for absolutely
essential Forms that must be converted, amber for "nice-to-haves" that can be
done as and when time allows and red for “bin it”.

Update your Formsweb.cfg configuration file as required. This file is where you
can put things like the start form for your app, the look and feel, window size
etc... The other config type files such as register.dat and default.env can also
be used for different configuration options such as the use of user-defined icon
for menu items.

Finally, we can now start the conversion process. It’s pretty much the same for
each form.

-   Open the V5 form in Forms 6 builder

-   Compile the form

-   Save as a V6 form

-   Open the V6 form in Forms 11g builder

-   Compile the form

-   Save the V11 form

-   You’re done!

Although we preferred to convert each form individually by hand as it were there
are utilities built into both Forms 6 and 11g builder that enable you to bulk
convert forms. It’s your call. If all has went well you *should* get similar
results to what we achieved.

The screen-shots below show an example of the same form - our main Stock Details
screen - running under both Forms11 (top image) and Forms 5 (bottom image). As
you can see they are very similar in look and feel which was our intention. We
took the opportunity under Forms 11 to re-arrange and tidy up our menu structure
and got rid of our short-cut menu icons. I mention in the next section some of
the potential issues that can affect buttons and you can see an example of that
near the bottom right hand side of the images where some of the button
colours/text under the Links section have changed from grey/black under Forms 5
to white/red under Forms 11.

![](https://github.com/taupirho/oracle-forms5-11-upgrade/blob/master/image7.png)

![](https://github.com/taupirho/oracle-forms5-11-upgrade/blob/master/image8.png)

**Potential issues**

What follows is a non-exhaustive list of issues you may or may not encounter
during your upgrade.

-   **Colour changes to Forms backgrounds and/or buttons and fields.**

    For reasons unknown the backgrounds in some of our Forms changed from a
    light grey colour to teal. Equally inexplicably some of the button colours
    and text changed to a red or white colour. In both cases you can simply go
    into the form under Forms 11g and change back as required.

-   **Frame/field/button/label sizes.**

    We found that for some Forms a few of the fields/buttons, labels and frames
    needed a touch of re-adjustment to get them to fit onto their place on the
    Forms window properly.

-   **Forms that call either reports or graphics.**

    These will need closer attention. Luckily we had only one form that used an
    Oracle Graph and, on closer inspection, found that it wasn’t really being used
    so we could simply scrap the part of the form that displayed the graph. If you
    can’t do this you will need to implement your graph as a JAVA bean. The
    demo pack that comes with Forms 11g has some examples of this. There is
    no direct equivalent to the old Oracle Graphics product under Forms 11g.

We also had about half a dozen Forms that called Oracle reports. For these it
was a case of opening the reports under the Reports 11g builder, compiling
them then saving them. We didn’t have to do the intermediate step of opening
them under Reports 6 but you may have to do this for particularly complex
reports. Ours were quite simple. Once the reports were in 11g format we
turned our attention to how they were being called from Forms. It’s likely
that your existing call to a report under Forms 5 will be something like
this.

```
Run_Product(REPORTS,'your_report.rdf', ASYNCHRONOUS,BATCH,FILESYSTEM,'paramdata', NULL);
```

Under Forms 11g there are a couple of changes you have to make. The first
thing you'll notice is that there is a new REPORT node in the object
navigator. In here you define certain aspects of your report, such as its
name, its associated filename, printer and so on. So do that here. The
report name is anything you like, say, **REP001**. Filename would be
**your_report.rdf**, execution type as **run** – say, report server name and
so on.

Once you have this you will need to change the call to run_product above to
be something like:

```
ReportServerJob VARCHAR2(254);
pl_id paramlist := create_parameter_list('paramdata');
add_parameter(pl_id,'P_1',text_parameter,:some_formfield_name_here);report_id := 
find_report_object('REP001');  
/* If you were printing the report out include the line below */
SET_REPORT_OBJECT_PROPERTY(report_id, REPORT_DESNAME, 'printer_device_name');
/* And the line below to actually run the report */
ReportServerJob := run_report_object(report_id,pl_id);

```

**Summary**

That’s pretty much it. For what it’s worth my advice is this. If you don’t need
to upgrade your forms app then don’t. However you’re probably just kicking the
inevitable down the road a year or two and at some stage you’re going to have to
go for it. There is also a lot of talk around moving to or converting to Oracle
APEX but again if you’re happy with Forms then I would go for the Forms upgrade,
it really isn’t as complicated as you might think and I hope this article is of
help to you. If you are at all worried about the future of Forms then perhaps
the following snippet from Oracle - taken from their Oracle Forms Statement of
Direction, last updated in March 2017 will assuage your concerns. NB The full
article can be accessed at https://support.oracle.com (requires a login
account). Search for Doc ID 2009262.1

**"Oracle continues its commitment to Oracle Forms.   New releases are being
planned and new features and other improvements are currently being reviewed. 
New releases are planned to include some of the following, as well as many
others:"**

-   Design-time productivity improvements

-   Application Deployment utilities

-   Performance improvements

-   New and enhanced object properties

-   New runtime UI features

-   New and improved integration with various products and technologies

-   Support for new Java versions

-   Support for new operating systems

>   And many more...

**"With Cloud Computing being such an important part of today’s IT landscape,
Oracle is investigating what possibilities the Cloud may offer for the Oracle
Forms product and its customers. Using Oracle Forms in the Oracle Public Cloud
could offer significant cost savings simply by reducing the typical cost of
hardware upgrades and maintenance. For Independent Software Vendors (ISV), and
other software providers, the use of the Oracle Public Cloud could make the
delivery and accessibility of Oracle Forms based applications much easier and
cost effective."**


**"Also, a significant part of modern computing is mobile technologies. Working
closely with Oracle Partners, Oracle will continue to investigate possible
mobile solutions and how they may apply to an Oracle Forms customer."**




Tom lives and works in Edinburgh for Euromoney Indices. He has been an Oracle developer on and off for over fifteen years.




![](https://github.com/taupirho/oracle-forms5-11-upgrade/blob/master/image9.png)   **https://www.linkedin.com/in/tom-reid/5a2a3a/**



![](https://github.com/taupirho/oracle-forms5-11-upgrade/blob/master/image10.png)   **thomas.reid\@gmail.com**
