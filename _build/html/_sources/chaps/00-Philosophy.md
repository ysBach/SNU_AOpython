Made by Yoonsoo P. Bach

* Ver 1.0: 2017 Feb
* Ver 1.1: 2017 Sep
* Ver 2.0: 2018 Mar
* Ver 2.1: 2020 Sep (add "A Note" section)
* Ver 2.2: 2023 Jul (LanguageTool grammar check; added "NOTE(2023-07-11)")


```{contents}
```

# Philosophy
All the lecture materials were first made for "**Astronomical Observation**" (AO hereafter) of the Dept. of Astronomy of Seoul National University in 2017. Then gradually updated as time went on.

* Lecturer: Prof. Masateru Ishiguro
* TA: Yoonsoo P. Bach


This preface contains some comments to explain the ground philosophy while making the lecture notes and the reason for transforming from the previous IRAF/IDL-based AO system to a Python3-based system.


## A Note

I was lucky to have opportunities to serve as a TA at the AO class at SNU for years. In 2016, I found that the systems of IRAF and IDL prevailing in astronomy will only hinder our development and lead our academic society to a future dependent on commercial companies; later, I also noticed the possibility of legal issues*. IRAF is a large black box, which indeed does not work as it is described in the manual or as we'd expect (from image combination and aperture photometry to the PyRAF problem**). People have been using it just because there was no alternative, and these hidden bugs do not give an error much larger than our expected precision, but we've got to regret that we didn't have systematic "testing modules" and just assumed it worked correctly.

I have tried my best to discard all pieces of IDL and most of IRAF and replace them all with Python. These are the skeletons for photometry and polarimetry tools I developed for my observational research†.

\* See 2.1.3 below. Also, P01 in ppt mentions this.

\*\* If you are using PyRAF (nothing but an IRAF wrapper in Python), I urge you to check if it REALLY does the calculation you intended. Many times, it does not update the internal parameters. Worse, it does not raise any warnings or errors, and pretends as if it did everything correctly. I had to abandon all of my research outputs in 2016 after I found that PyRAF does not even do aperture photometry correctly. (Many are confused, so a note: PyRAF is not astropy)

† SNUO1Mpy; TRIPOLpy; NICpolpy; Bach, Ishiguro, Jin et al. 2019 (4179 Toutatis, JKAS); Takahashi+2020 (submitted); Pravec et al. (1999 KW4, in prep), Ishiguro, Bach, Jin et al. (2005 UD, in prep); Bach, Ishiguro et al. (Vesta and Ceres, in prep), Craig, McCully, Conseil, Bach (astropy Image-combination in ccdproc); Geem, Ishiguro et al. (1984QY1+, in prep); Heintz and Finbo ([PyReduc](https://keheintz.github.io/PyReduc/))


## Choosing Software and Programming Language

### Problems of IRAF
For decades, IRAF by NOAO has become a virtual standard for astronomical image reduction processes. As with any other tool, it has also had some problems, and I want to point out three of them.

1. No human power exists to develop or maintain IRAF
2. IRAF has some problems since it's "old" software.
3. IRAF has some license issues, and thus the documentation issue happened in the summer of 2019.
4. Major new facilities in the near future will not use IRAF/IDL

The last one will be explained later, since it includes IDL too.

#### Human Power Issues

The most important problem is that “required” updates of IRAF may not be there after version 2.16.1, at least officially, and **STScI is now decreasing human power for IRAF development and maintenance**.

IRAF and its core SPP and CL are designed purely for IRAF society. There are virtually no computer scientists, programmers, or even astronomers who have an interest in developing new codes using SPP now. You may not even have heard of the name SPP before. In other words, we cannot hope for IRAF to be fixed or updated even if there are serious or unexpected bugs or problems. Few such problems are actually there, and some are documented by STScI (go to a doc page of some IRAF task, e.g., [``IMEXAMINE``](http://stsdas.stsci.edu/cgi-bin/gethelp.cgi?imexamine): you can see a section called ``BUGS``). Some bugs include an inaccurate aperture sum or wrong pixel value display if you use ``DISPLAY``.

**NOTE** (2023-07-11): Even STScI shamelessly and irresponsibly shut down their documentation service for IRAF in mid-2019. As they did not even provide an archive of the documents, all the links used in multiple educational/research materials became garbage in one day, including this lecture note. That was why the [IRAF-community](https://iraf-community.github.io/) emerged, and a new [documentation hosting site](https://iraf.readthedocs.io/en/latest/index.html) appeared after painful efforts. Be aware that even this international research institute can make such stupid and aggressive decisions and *vandalize* scientific literature. That's why it is important to choose a tool that has a bright future in the beginning and make yourself prepared to change the tool at any time in your career (don't blindly believe Python either, although it should be more trustworthy than, e.g., STScI). Otherwise, if your tool is destroyed, no one will save you.

Even though such serious problems may be alleviated in the future, it is almost hopeless to “optimize” IRAF. There are a plethora of different ideas and algorithms to make computation faster that are still being studied by frontier computer scientists, yet they may not be implemented in IRAF.

This is critical, considering one single astronomical image can now be a gigabyte. My favorite example is [L.A. Cosmic](http://www.astro.yale.edu/dokkum/lacosmic/), which is a tool for removing cosmic rays from astronomical images. We have both IRAF and Python versions (Python version from `astroscrappy` actually uses C++, though it is fully usable on Python), and the Python version is an order of magnitude faster than that of IRAF version. It can be up to ~100+ times faster, not only because it works efficiently, but also because IRAF does not support efficient parallel computing by default. One of my colleagues had to spend ~ 0.5 days doing L.A.Cosmic to all their FITS data. If there was anything wrong with the results, you have to spend 0.5 days again. The amount of data we have to analyze increases exponentially over time. LSST, which will survey all-sky with a depth of Subaru, or JWST, which is the next-generation HST, decided to use Python 3 and C++ against IRAF/IDL (see below).


#### IRAF is Old

The second problem with IRAF is that it is **old** software, i.e., a problem that any other programs may have if they were developed decades ago, and it includes:

* Works only in a 32-bit environment.
   * So we need some tricks to run IRAF on modern-standard, 64-bit, computers.
   * This treatment is required almost exclusively for IRAF. Most modern pieces of software do not require a 32-bit environment.
* Works only on UNIX-based OS
   * It is bothersome for the majority of Windows computer users to have redundant Linux computers for IRAF purposes unless MS fully supports a UNIX-like environment.
* CL has no debug functionality, so it is bad for large programming.
 * Each observatory needs its own software for data reduction. SPP and CL are not good for such purposes.
 * Actually, many different observatories make their own routines with IDL, not IRAF/SPP/CL, and distribute them to the observers.



#### License and Documentation Problems

IRAF seems to have a copyright problem that can be sensitive to mention; I hesitate to even mention it. If you are interested, you may search for [iraf-community distribution](https://iraf-community.github.io/) (which became public to the open source community thanks to the years of hard work of Dr. Streicher). I heard from third parties that this may have been one of the reasons why NASA and STScI started to hide IRAF from the main pages and tried to erase many traces of it. You may remember STScI's removal of IRAF documentation hosting in the summer of 2019 (I harshly blamed it as an irresponsible vandalism action in science done by an institution that once urged scientists to use IRAF).



### PyRAF and Its Problems
PyRAF is an alternative, made of Python, to avoid those problems of IRAF, especially to alleviate the complexity / debugging problems of SPP/CL:

> Excerpt from [STScI](http://www.stsci.edu/institute/software_hardware/pyraf/stsci_python):
>
> PyRAF is a command language for running IRAF tasks that is based on the Python scripting language.

According to this [Link](http://iraf.net/forum/viewtopic.php?showtopic=97713&lastpost=true), the attempt to replace CL with Python was ongoing in 1999. [This report](https://arxiv.org/abs/1610.03159) (arXiv 1610.03159) says such attempts at using Python started in 1998. Before 2000, when PyRAF was first made, IRAF was not even free software, so using completely free Python is a very attractive trial. PyRAF works on Windows, too, though it has many problems since some functionalities were designed only for UNIX systems *from the beginning*, as is also clearly noted by STScI.

This PyRAF has some serious problems. First, **identical CL scripts give different results in PyRAF in some cases**. This is implicitly explained in the [PyRAF Tutorial](http://stsdas.stsci.edu/pyraf/doc.old/pyraf_tutorial/PythonPrereq.html), too: *"You can even run more than 90% of IRAF CL scripts!" (in PyRAF)*. From a different perspective, it means ~ 10% of CL scripts do not work identically compared to those of IRAF. I have such experience with photometry that I had to do everything again from scratch. So if you have CL scripts you made when you were using IRAF, you wouldn't want to take the risk of using PyRAF. You don't know which part will give a different result from the original IRAF! No warning system for indicating the difference between IRAF and PyRAF has been developed, so you have to run the script on IRAF and check whether it works correctly on PyRAF, which is just bothersome and inefficient.

Other than that, PyRAF was written in Python 2, and unlike many other Python packages, it has a small amount of manual, which makes it difficult to start and use efficiently. You wouldn't want to move to PyRAF, which doesn't seem to be *much* better than IRAF CL scripting. One of the important issues of PyRAF is the same as that of IRAF: **No big update will be made in the near future**, though we have many known bugs. It is difficult to manually check how much budget and human resources are being used for PyRAF, but you can compare the frequency of updates between PyRAF and Astropy. Although PyRAF is being updated for a certain period of time, we are not sure how long it will be maintained. Lim Pey-Lian, at the Science Software Branch of STScI, replied to my question about PyRAF via the official help email account that :

> "We are phasing out our IRAF support. Could you please try out photutils, a Python replacement for photometry tasks ..."

### IDL and Its Problems
These are logical reasons why many people seek IRAF alternatives. **One of such solutions is IDL**. Easier scripting, OS-independent, and the fact that it is maintained by software experts made astronomers relieved and rely on this software. However, IDL also has some problems: The most important one is the **license issue**, and secondly, the problem as a programming language (see [here](https://en.wikipedia.org/wiki/IDL_(programming_language)#Problems)). Also, it is slow when plotting graphs, especially 3-D plotting. Another important issue is that it is used by very minor groups of people in astronomy and bio-imaging, so there are not many structured manuals we can access: same as IRAF!

Licenses are very critical for users, not only because of their price (SNU has spent a budget for IDL licenses comparable to that used for rebuilding the telescope at Building 45), but also because you have to purchase a license **each time** there is an update. You cannot use IDL 8.1 even if you have a license for IDL 8.0. Institutions will be burdened with this since many researchers should use the software, and the amount of required budget will go up almost linearly. If the institution does not support a license fee, the compatibility problem arises: Say you have source code written in IDL 8, but the institution has a license for IDL 7 only. Nevertheless, it is widely used at observatories and institutions, due to its convenience for scripting compared to IRAF.

In July 2017, there was a big happening at SNU: the IDL license policy changed, and thus all the PCs of students and professors could not use IDL for a few days (IDL license needs an Internet connection, and the company shut down the older license without properly noticing it). More seriously, the updated license has a policy that does not allow more than 100 tabs. I said tabs, not computers. So if 10 users are using the SNU license and turning on 10 tabs each, no more people can use IDL. This is the weakest point of all commercial pieces of software. Especially if it is a programming language, it is a disaster.

The same thing has happened almost every 2-3 months since then (added 2018 March).

### Examples of IRAF and IDL Usage

* NASA IRTF: You will get IDL source codes to analyze SpeX (spectroscopy observation instrument) from IRTF.
* Nayoro Pirka, Japan: One of the few cases of using IRAF. If you use MSI (multi spectroscopy imager), you will use the MSIRED package, which is written fully in IRAF CL.
* Subaru: If you used FOCAS of Subaru at Hawai'i, you will receive the FOCASRED package, which you may use either IDL or IRAF (recommended using both).


### New Major Facilities Want Freedom from IRAF

"New Major Facilities" includes HST, JWST, LSST, and future space telescopes and/or large projects.

STScI, which will be the [Science and Operations Center of JWST](https://en.wikipedia.org/wiki/James_Webb_Space_Telescope#Ground_support_and_operations), as well as HST, selected Python 3 for their data production. They are now developing pipelines for JWST ([github](https://github.com/STScI-JWST/jwst)). All the new packages from STScI, e.g., the famous DRIZZLEPAC (now named AstroDrizzle), support Python only (preface and Section 5.1 of this [handbook](http://documents.stsci.edu/hst/HST_overview/documents/DrizzlePac/drizzlepac.pdf)). The reason for choosing Python for the new era is described in the 2013 JWST Roadmap, e.g., [Section 3.5](http://www.stsci.edu/~ferguson/roadmap/html/intro.html#multiple-interfaces) and why we should avoid IRAF in general in [Section 4.2](http://www.stsci.edu/~ferguson/roadmap/html/intro.html#remove-scientist-dependency-on-iraf).



```{admonition} Python2
:class: dropdown
The LSST data management team has chosen to use Python 3 and C++ ([main DM page](http://dm.lsst.org/)), although they will support Python 2.7 for a few years but not long. The scientists and engineers working for these large projects are contributing to astropy core packages, too.


The note below was written in 2018. As of 2023, Python 2 is dead.

Python 2 will cease to be supported soon. See, e.g., [PEP373](https://www.python.org/dev/peps/pep-0373/), [Python3Statement](http://www.python3statement.org/), [python clock](https://pythonclock.org/) made by Guido (The inventor of Python). All the astropy and its affiliated packages, matplotlib, and many more which you are familiar with, will not support Python 2 within only a few years (even before you get a PhD!).

For useful things in Python 3 compared to 2, see [Python 3 for scientists](https://python-3-for-scientists.readthedocs.io/en/latest/python3_user_features.html).
```


### Python: An Alternative

If you are a very smart person, you can, of course, directly make a binary code (11000101011101000010101101...), and this will be the most efficient way. When I was an undergrad student at KAIST, there was this professor who taught the Introduction to Programming course, which was mandatory, who said, "I had a friend who believes it is the best way to make all the programs in binary from the beginning. But the thing is, not everyone is as smart as they are."

The second-best option might be using some low-level languages, such as C, or making appropriate modifications to pre-existing IRAF to make it more efficient/faster. The thing is that we have to think about the **vast majority of people who are not** that smart (plus, as mentioned above, there is no one interested in developing IRAF). This is because we do not have much room to lose such people who may serve important roles in the future of astronomy. Quite a few scientists are also involved in that *vast majority*, and they are not (and likely should not) practicing being programmers.

Thus, the next option we may choose, are languages which have the following strong points:

1. Easy to learn and coding (scripting),
2. i.e., "a language that more people can participate/contribute"
3. Reliable, since they are maintained, managed, stabilized, and optimized by computer scientists / programmers / experts,
   * i.e., "a language which is also being used by non-astronomers"
4. Have no license problem,
5. OS-independent (if properly coded),
6. Have good and enough amount of manual/examples,
7. Have a sufficient number of useful packages reliably,
8. And have many users so that faster feedback/Q&A can be made.
   * e.g., stackoverflow
9. Additionally, languages have the potential to have connections with future languages, so learning them can be an investment in our future.

**Python is a language that satisfactorily meets all the above conditions**

It is not easy for laymen to use SPP/CL, as it has no highlighting or syntax coloring functionality in any of the IDEs and has complicated manuals. For the third item, Python is completely free and is installed by default on virtually all Linux distributions, so no license issue can arise for Python users. The fourth and fifth items are strong points of Python, on which most Python users would agree. If you use Anaconda or Git, you can manage and update packages even on Windows via shell script, and the Spyder IDE is provided by default. Python is well known for having well-structured manuals.

The sixth item means NumPy, SciPy, Matplotlib, and AstroPy, which we will learn throughout this course. They are specialized in the majority of numerical calculations and graph plotting. The seventh item can be checked at websites such as Stack Overflow. A tremendous amount of manuals and Q&A databases can be found, incomparable to IRAF, and there is also more diversity since non-astronomers also use it, unlike IDL.

Finally, Julia, Jupyter, Markdown (this preface is also made from md), and many future pieces of software use Python-like grammar or implement ways to use Python. Python is good for large-data analysis, too, which is the future of astronomy. The strong point of Python with Jupyter can be seen throughout this TA class. Although it is commercial, you can also find Python tools attached to Excel to make neat graphics without errors. We may need a long time to see Python fade into the past.

### So... Use Python

Some astronomers dreamed of using Python for these reasons. That is now known as the Astropy Project. Alongside the "core" package, astropy, there are other packages that are listed under the name "Astropy Affiliated Packages". You can easily manage versions of packages and update them using Anaconda, and it is extremely easy to fix bugs compared to IRAF/IDL. Astropy itself also provides neat and clean examples and manuals.

Additional benefits we may get from using Python include learning how to be prepared for future environments. Even if Python becomes deprecated and a new language becomes the new *standard* someday, you can easily adapt to that new environment. On top of that, a new future language can become easier than Python, but not the other way around; your value will get higher in that era as you can cope with more languages.

However, this does not mean Python has no problems. There are quite a few packages that are not stable (no stable release), even though they are considered as "widely used" and "working perfectly". Core packages of Astropy works fairly well, but there are some [known issues](http://docs.astropy.org/en/stable/known_issues.html). Photometry and spectroscopy tools are still 0.x versions. Only photometry version (photutils) is considered as "stable" from [Astropy affiliate website](http://www.astropy.org/affiliated/). Critically, LSST and JWST are not operating yet, so all the packages are not really completed yet.

## Transformation into a New Curriculum and Possible Pitfalls

I have briefly introduced why astronomers started using Python. You may have understood that it is at least worth considering the reformation of the structure of AO practice sessions, which had been using IRAF or IDL. The basic philosophy on which I am trying this new transformation is simple: The belief that education should let students be prepared for their future, and this should be one of its primary goals.

**I know that it may be comfortable and easy to teach IRAF as the TA.** But I wonder whether it really is an efficacious curriculum if students realize the existence/importance of Astropy *after* they graduate, while the school is teaching only IRAF. Now that the Computational Astronomy course at SNU has changed its main programming language to Python from IDL, I guess this will be a great opportunity to create a flexible Python environment at SNU in the future.
(The note above was written in 2019. As of 2023, I am happy to see virtually all students equipped with Python skills.)

One thing that scares me is the **possibility of a big gap between the generations who've learned IRAF and AstroPy**. I cannot say it is like using a photoplate after the implementation of CCD, but it feels like it. As an example, the terms used in `Ginga` are slightly different from those in DS9, so it's quite confusing if you are used to SAO DS9. Now my role, as a TA who's earning money from this work, is clear: While contributing to the construction of a new educational curriculum that fits better to prepare for the future, I have to amalgamate the gap between generations to avoid excessive inefficiency and loss of human power in communications between those generations. It might be tough for young students because they should take on the redundant burden of older generations in communication, but this kind of pain has been there since we transformed from cgs to the SI unit system. The reason for the widely used cgs values in the astronomical field seems to be due to the low weighting given to the "efficient communication with other fields of sciences" factor, but I worry that this might have become a giant wall that isolates astronomical society. Some other critiques of this Python trial may be that this astropy, and even Python, will become a *legacy of the past* somewhere in the near future. I do, however, worry much more about the severance between our generations and much younger generations in the future if we choose not to give them enough opportunity to experience the frontier of modern (computational/observational) astronomy, just because IRAF has been the "historical standard". Also, a scientist should be fluent in multiple "languages" (different unit systems, computing tools, scripting, etc.). Otherwise, you will be extinct anyway.

My basic reason for this is that not all students are smart enough to find and "experience" such things by themselves, as I am. If I didn't have personal antipathy toward the observation course, which I took right after I became a graduate student, I would never dare to find pieces of software other than IRAF. Not because of other reasons, but because I had had too many things to digest by myself at that time compared to my ability, so I had little mental space to think about other issues than the coursework itself. I believe this kind of effort to help them with modern technology is inevitable to maximize the efficiency of students like me.

It does not mean I will not deal with IRAF (but I will not deal with IDL at all): It is up to you to choose which one you use (IDL, IRAF, DS9, Astropy, ...), and I personally think that the knowledge on using IRAF is quite essential at least until the next decade. This is because

1. Many observatories still use IRAF as their default.
   * You may see some computers at observatories that are prepared for those who want to primitively check the observed data using only IRAF).


2. The importance of smooth communication between older and younger generations in this field.
3. Historically important.
   * Even if we use computers for observation, some information on CCD and even photoplate can help us understand the reasons for certain calculations and processes.

In short, it stems from the faith that **we should avoid radical discontinuity between generations** while guaranteeing the freedom to choose language/software. Also, I have to keep in mind that the IRAF tutorial should not be a big burden for students who learn Python.

The reason I am preparing this manual is not only because I believe that this new trial must not end as a one-time trial but also be continuous in time domain such that the transition from an older to a younger-fashioned way is smooth enough that not much social cost is spent in the future.

Now the task left in front of me is to make this manual easy and simple with readability, yet contain enough information so that the next students and TA/instructor can benefit from it. **A new TA or educator in the future who will take on the holy responsibility of education, which is strict and priceless, must critically evaluate this new trial with long and tortuous consideration, investigation, and continuous effort. Then they should decide how they will write the new future in front of them.**

Even though the students who took this course might become the only isolated people using astropy, this manual should totally be abandoned *if* an unassailable disadvantage, inefficiency, or problem is found in this curriculum. That is totally my fault, who will have been the former TA, and that is the reason why I cannot skip the lectures on practicing IRAF and SAO ds9. If that unfavorable situation occurs, then we have to go back to the previous IRAF/DS9 + IDL curriculum. For it not to be that way, my effort to clarify whether it is a reasonable trial worth doing is the most important.

I will do my very best for this course to be remembered as a part of your "good memory" during this year. If it turns out that it's not, then it's totally my fault, and I beg your cool-headed criticism and feedback.


At 19-dong of SNU,

First written on the verge of winter 2017,

Updated on the fringes of summer 2017,

Updated on the first day of the 2018 spring semester,

Yoonsoo P. Bach




