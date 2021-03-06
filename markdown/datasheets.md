Guide to finding datasheets and avoiding malicious datasheet SEO sites
======================================================================

Getting datasheets used to be easier, but it’s gotten harder on the
modern web due to SEO scum.  Google is almost useless.
<https://yandex.ru/> gives much better results for datasheet searches.
(Compare the results for searching [bp2842 datasheet] on the two
engines: Yandex gives you datasheets for the TI TL2842BP, which may be
a slightly wrong chip, but Google gives you random bullshit.  The term
“Даташит” may be helpful,) However, you run into CAPTCHAs occasionally
on Yandex.  For non-obsolete parts, Digi-Key is often a better source.
Still, though, you need to blacklist some providers.

Yandex puts a link saying “pdf Посмотреть” after search results that
actually point to PDFs.  Do not follow this link; it goes to
docviewer.yandex.ru.  But it does allow you to distinguish PDF links
from fake links.  (Except that the SEO scum sometimes generate fake
PDFs.)

Most of the sites mentioned have their own search engines.

Octopart has its own search engine, and it’s useful if you want to buy
things, and it does provide datasheet links, but for example
[searching for bp2812 on Octopart][22] gives you sealed lead-acid
batteries.  With a datasheet, mind you.  Searching for “c3205” (the
marking on the common 1990s 2SC3205 transistor) similarly produces no
useful results.

[22]: https://octopart.com/search?q=bp2812

Known-good sources
------------------

* datasheetspdf.com: iframe with PDF datasheet accessible from the
  “Download Foobar3103 datasheet” link as well as the “Foobar3103
  datasheet” links in the left column.  [C3205, for example][0].
* ndatasheet.com: alternate domain for datasheetspdf.com, saying, “The
  site has been moved : DatasheetsPDF.com” at the bottom.  [BP2812,
  for example][20].
* chipdip.ru
* datasheet-pdf.com: iframe with PDF datasheet in third iframe on
  page.  [C2878, for example.][4] Decent filenames too.  Apparently
  scraped from datasheet4u.
* njr.com: direct links to PDF show up in Google, but only for their
  products.  [NJM4565, for example.][6]
* Mouser: datasheet link after “Datasheet:” saying “Foobar3103
  Datasheet (PDF)”, but only for current products.  [NJM4565, for
  example.][7]
* digchip.com: iframe “ifr data” on page reached from “Download
  Foobar3103 datasheet” link; [BA5936S, for example][11].
* datasheet4u.com: iframe with PDF datasheet on page reached from “PDF
  Download: *[IMG] Foobar3103 datasheet PDF*” link, same as
  datasheetspdf.com.  [C3199, for example.][12]
* kazus.ru: iframe “datasheet pdf” contains PDF datasheet with
  unreasonably long filename, on page reached by posting “[
  Foobar3103.PDF (338 Kb) ]” form.  [TL2842BP, for example][14].
* rlocman.ru: link “Скачать” to vendor’s site via a redirector.
  [TL2842BP, for example][15].
* power-on.tech: links labeled “Datasheet Foobar3103” and “Скачать
  Datasheet Foobar3103” on main page.  [UC2842B, for example][16].
* chinesechip.com: PDFs directly linked from Google, albeit with goofy
  GUID firenames.  [BP2812, for example][18].
* ibselectronics.com: PDFs linked directly from Yandex, with good
  filenames.  [BP2832, for example][21].
* onsemi.com: PDFs linked directly from Yandex and Google, with good filenames,
  but only for ON Semiconductor products.  Note that they’ve recently
  fucked us over by breaking the Fairchild links from Digi-Key.
* st.com: PDFs linked directly from Google, with good filenames, but
  only for ST products.  With shitty filenames.
* alltransistors.com: iframe and “Abrir como PDF” links of the form
  <https://alltransistors.com/pdfdatasheet_panasonic/2sd1512_e.pdf> on
  “Foobar PDF datasheet” page with URL of form
  <https://alltransistors.com/es/pdfview.php?doc=2sd1512_e.pdf&dire=_panasonic>
  on link of form “2sd1512_e.pdf” on “Foobar
  . Datasheet. Equivalente. ...” page linked from Google, for example,
  [2SD1512](https://alltransistors.com/es/transistor.php?transistor=18229).
* datasheetcafe.com: link under header “Foobar Datasheet” labeled [
  FOOBAR.PDF ] linking to link of the form
  <http://j5d2v7d7.stackpathcdn.com/wp-content/uploads/2015/09/TT2140.pdf>,
  for example, [TT2140][24].
* yoreparo.com: people post service manuals in the forums.
* tvsat.com.pl: datasheets directly linked from Google, e.g., [2SB985][26]
* datasheet.octopart.com: datasheets directly linked from Google,
  e.g., [2SA1015][27]
* pdf.voron.ua: datasheets directly linked from Google, e.g., [2SA984][28]
* vishay.com: datasheets directly linked from Google, but only for
  their products (including old Siliconix parts)
* nxp.com: similarly, but including old Freescale and Philips parts;
  e.g. the [mc9s08sg32][30]
* infineon.com: similarly, but including old International Rectum
  Fryer parts
* irf.com: similarly, though they redirect to infineon.com (with a
  working link to the PDF)
* datasheet.lcsc.com: similarly, but they carry a huge array of
  current parts, including many even Digi-Key doesn’t; for example,
  the [HT7333][29]

[0]: https://datasheetspdf.com/pdf/1405124/SeCoS/C3205/1
[4]: http://www.datasheet-pdf.com/PDF/C2878-Datasheet-ToshibaSemiconductor-634662
[6]: http://www.njr.com/semicon/PDF/NJM4565_E.pdf
[7]: https://www.mouser.com/ProductDetail/NJR/NJM4565L?qs=cYKsvIpO1PijlM%2FDhzFbCA%3D%3D
[11]: https://www.digchip.com/datasheets/parts/datasheet/406/BA5936S.php
[12]: https://www.datasheet4u.com/datasheet-pdf/JSL/C3199/pdf.php?id=91129
[14]: http://kazus.ru/datasheets/pdf-data/4298625/TI/TL2842BP.html
[15]: https://www.rlocman.ru/datasheet/data.html?di=174635&/TL2842BP
[16]: https://power-on.tech/datasheet-%D1%82%D0%B5%D1%85%D0%BD%D0%B8%D1%87%D0%B5%D1%81%D0%BA%D0%B0%D1%8F-%D0%B4%D0%BE%D0%BA%D1%83%D0%BC%D0%B5%D0%BD%D1%82%D0%B0%D1%86%D0%B8%D1%8F-uc2842b/
[18]: http://www.chinesechip.com/files/2015-06/d271a84d-12eb-4cd0-b0bb-3accb73bee96.pdf
[20]: http://www.ndatasheet.com/noconverter/1018976/BPS/BP2812-pdf.html
[21]: http://www.ibselectronics.com/ibsstore/datasheet/BP2832A_EN_DS_Rev.1.0.pdf
[24]: http://www.datasheetcafe.com/tt2140-datasheet-sanyo/
[26]: https://www.tvsat.com.pl/pdf/2/2sb985_san.pdf
[27]: http://datasheet.octopart.com/2SA1015-Y(F)-Toshiba-datasheet-9586966.pdf
[28]: https://pdf.voron.ua/files/pdf/tranzistor/2SA984.pdf
[29]: https://datasheet.lcsc.com/szlcsc/1810171710_Holtek-Semicon-HT7333-A_C21583.pdf
[30]: https://www.nxp.com/docs/en/data-sheet/MC9S08SG32.pdf

Broken at the moment
--------------------

* ru.datasheetbank.com

Malicious but sometimes usable if nothing else works
----------------------------------------------------

* datasheetarchive.com: iframe with PDF accessible via “PDF” link in
  “PDF” column — but for the wrong part!  [A1286, for example][1].
  However, I *did* get the right datasheet for [CXA1498S][10].
* kynix.com: links to alldatasheet.com, for example for [STP7N60FI][23].

[5]: https://www.alldatasheet.es/datasheet-pdf/pdf/35940/ROHM/BA3126N.html
[13]: https://www.alldatasheet.com/datasheet-pdf/pdf/168231/TI/TL2842BP.html
[23]: https://www.kynix.com/Detail/38114/STP7N60FI.html

Blacklist; _never visit_ (at least if you want the datasheet)
-------------------------------------------------------------

* radiolibrary.ru: provides lots of information in HTML, in Russian,
  but no datasheet
* datasheetq.com: iframe “contentpdf” on “DOWNLOAD” link redirects to
  home page.  [A1286, for example][2].
* web-bcs.com: refreshes to page with no PDF; [A1286, for example][3].
* datasheet.es: not only no PDF link, but malicious SEO alt text
  (“Foobar3103 arduino”) on links to PNGs that have *had all the text
  removed*.  “PDF descargar” link with “download.php?id=foobar” points
  to HTML page containing only malicious cloaking JavaScript
  redirecting you to the HTML page.  [CXA1498S, for example][8].  Does
  have text ripped from the PDF in HTML, though.
* datasheet26.com: same as datasheet.es, but in Russian.  [BP2812, for
  example][19].
* datasheetcatalog.com: no PDF link; link labeled “Download
  *Foobar3103 pdf datasheet* from FOOCORP” is JS, linking to an URL
  ending in “.pdf”, but that page is HTML and just links you back to
  the original page and similar ones.  [CXA1498S, for example][9].
* y-ic.com: generates PDFs with no actual information about the chip,
  containing only advertisements.  [BP2812, for example][17].
* transistordata.com: PDF pages are 404; also has hits for things it
  doesn’t have datasheets for
* assets.nexperia.com: 403 Forbidden for wget
* worldwayelec.com: generates fake datasheet PDFs containing only ads,
  has no actual information on parts; [AVC479, for example][25].
* alldatasheet.es/alldatasheet.com: previously quite difficult: PDF with
  application/octet-stream content-type and .html URL ending,
  accessible via form POST of “[ Download ]” button, on page
  accessible via “Download” link to URL of form
  `https://pdf1.alldatasheet.es/datasheet-pdf/download/35940/ROHM/BA3126N.html`,
  in a locked filing cabinet with a sign saying “Beware of the
  jaguar”; [BA3126N, for example][5], or [TL2842BP][13].  Was a last resort.
  Now the button says “[ If You Want to View Datasheet, Click To Here !! ]”
  instead, but doesn’t work.
* ic-components.com: generates fake datasheet PDFs containing only
  ads; [AS12W-K][31], for example.

[1]: https://www.datasheetarchive.com/A1286-datasheet.html
[2]: http://www.datasheetq.com/datasheet-download/219014/1/Isahaya/A1286
[3]: https://www.web-bcs.com/transistor/tc/a0/A1286.php?lan=en&cl=1
[8]: http://www.datasheet.es/PDF/199122/CXA1498S-pdf.html
[9]: http://www.datasheetcatalog.com/datasheets_pdf/C/X/A/1/CXA1498S.shtml
[10]: https://www.datasheetarchive.com/CXA1498S-datasheet.html
[17]: https://www.y-ic.com/pdf/dc/5383541-BP2812.pdf
[19]: http://www.datasheet26.com/search.php?sWord=BP2812
[25]: https://www.worldwayelec.com/pro/sanyo/avc479/3488317
[31]: https://www.ic-components.com/products/caf694907/AS12W-K.pdf

Transistor part numbers
-----------------------

kludge explains:

> There are three religions: Japanese numbers, Pro-Electron numbers,
> and American JEDEC numbers. Japanese numbers all start with 2S so
> they don’t bother printing the 2S part.  American ones start with 2N
> but they print it.  Pro-Electron ones have two letters for type and
> then more numbers.

So if you have a Japanese transistor that says C3205 on it, maybe look
for 2SC3205.

Surface-mount parts are a bitch.  There’s a book I’ve seen
somewhere...