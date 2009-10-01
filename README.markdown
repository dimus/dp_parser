# dp_parser

This is a library for extracting the date of publication contained in MEDLINE records into a Date object. 

The Date of Publication (DP) field contains the full date on which the issue of the journal was published [field description](http://www.nlm.nih.gov/bsd/mms/medlineelements.html#dp). The good news is, the values of that field are in a standardized format. The bad news is, there are twelve different variations!

The variants break down into two categories: absolute dates, and date ranges. Absolute dates describe a specific day, such as 1984 Apr 30. Not very difficult to create a Date object for that!

However, many MEDLINE records do not contain an absolute date, but a date range. For instance, a bi-monthly journal might have a Date of Publication of 1984 Apr-May. A quarterly journal might have a date of publication of 1984 Spring-Summer. A weekly journal might have a date of publication of 1984 Apr 23-30. And so forth. These can be significantly harder to resolve into a Date object.
  
dp_parser does this for you.

## Known Variations of Absolute Dates
* 2009 [example](http://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=pubmed&retmax=1&retmode=medline&rettype=medline&id=19667745)
* 2009 Jun [example](http://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=pubmed&retmax=1&retmode=medline&rettype=medline&id=19470686)
* 2009 Aug 4 [example](http://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=pubmed&retmax=1&retmode=medline&rettype=medline&id=19651618)

## Known Variations of Date Ranges
* 1977 May-Jun [example](http://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=pubmed&retmax=1&retmode=medline&rettype=medline&id=904940)
* 1983 Feb 17-23 [example](http://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=pubmed&retmax=1&retmode=medline&rettype=medline&id=6298629)
* 1990 Spring-Summer [example](http://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=pubmed&retmax=1&retmode=medline&rettype=medline&id=2076220)
* 1991 Nov 21-Dec 4 [example](http://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=pubmed&retmax=1&retmode=medline&rettype=medline&id=1754152)
* 1995-1997 [example](http://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=pubmed&retmax=1&retmode=medline&rettype=medline&id=11638937)
* 1974-1975 Winter [example](http://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=pubmed&retmax=1&retmode=medline&rettype=medline&id=10237052)
* 1981-1982 Winter-Spring [example](http://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=pubmed&retmax=1&retmode=medline&rettype=medline&id=7346545)
* 1995 Dec 14-1996 Jan 11 [example](http://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=pubmed&retmax=1&retmode=medline&rettype=medline&id=8696106)
* 1993 Dec-1994 Jan [example](http://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=pubmed&retmax=1&retmode=medline&rettype=medline&id=7660197)

## How it Handles Date Ranges

We handle date ranges by assuming the first date we find is the one on which the journal was published. For instance, the May-June issue of a journal comes out in May, right?

This may not necessarily hold true for records that have a Date of Publication like "1995-1997", however the EDAT of [this record](http://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=pubmed&retmax=1&retmode=medline&rettype=medline&id=10237052) backs up our assumption, so I'm rolling with it. If you find a MEDLINE record that doesn't conform to these assumptions, please let me know.

## How it Handles Seasons

Seasons present a particular challenge. For instance, "1974-1975 Winter". What does that even mean? When we encounter a date with a season, we just set it to the first day of that season. So, "1974-1975 Winter" becomes the first day of winter, 1974: 1974 Dec 21.

## Installation

    sudo gem install rschenk-dp_parser


## Usage

The two methods you're interested in are to_h and to_date.

    p = DatePublishedParser.new
    result = p.parse('1977 May-Jun')

    result.to_h
    => {:year => 1997, :month => 5}

    result.to_date.to_s
    => 1997-5-1

Notice that when you call to_h, it only fills out the fields that it knows. In the example above, there is no :day field in the hash, because the given date does not specify a day.

However, when you call in to_date, it will fill in the missing fields. The month defaults to Jan, and the day defaults to 1. Just like the Ruby Date object. (Imagine that!)

## Note on Patches/Pull Requests
 
* Fork the project.
* Make your feature addition or bug fix.
* Add tests for it. This is important so I don't break it in a
  future version unintentionally.
* Commit, do not mess with rakefile, version, or history.
  (if you want to have your own version, that is fine but
   bump version in a commit by itself I can ignore when I pull)
* Send me a pull request. Bonus points for topic branches.

## Copyright

Copyright (c) 2009 Ryan Schenk. See LICENSE for details.