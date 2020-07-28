A possible ambition for Derctuo is to include all the background
information needed to understand it, if I can find freely-licensed
sources.  So, for example, [Pandemic Collapse](pandemic-collapse.md) talks about
geography (the US, Tenochtitlán, Cambodia), historical events (the
Vietnam War, the 1918 flu, the Bronze Age Collapse), economic concepts
(unemployment, insurance, banks), and other institutions (the US DoD,
the Mormon church, major corporations).  [Solar furnace CPC](solar-furnace-cpc.md)
talks about physical properties of common materials, the
Stefan–Boltzmann law, manufacturing processes of ceramics,
thermodynamics, units of measurement, basic optics, and the structure
of the solar system.  [CCN Streams](ccn-streams.md) talks about networked
systems architecture, hashing, SHA-256, TCP/IP, disks, telephone
networks, and all kinds of programming stuff.

What is the body of knowledge that would be needed to make sense of
all this stuff?  Consider the Stefan–Boltzmann law.  To make any sense
of the statement *j* = *σT*⁴ you need to know algebraic notation and
what energy and temperature are, including the concept of absolute
temperature.  And you need to understand how solid objects have
surface areas.

Possible sources include MIT OpenCourseware, Wikipedia, Wikibooks,
cnx.org (before it shuts down), OpenStreetMap, Project Gutenberg, the
Internet Archive etexts collections, and for recent things, PLoS and
arXiv.org.  [Boundless] used to have some open-content textbooks but
they seem to have mostly been lost, though fragments like [their
definition of limits][8] survive in part.  [OERCommons] has a search
engine over thousands of freely licensed educational resources, of
which nearly a thousand few are [textbooks][5], such as [Jim
Hefferon’s linear algebra book][4] (CC-BY-SA, 7.5MB, 507pp.).  They
also link to OpenStax (which I’d forgotten about), Delft OCW, CMU OLI,
and another dozen or so similar initiatives.

[Boundless]: http://web.archive.org/web/20150711143053/www.boundless.com/textbooks/
[OERCommons]: https://www.oercommons.org/
[4]: https://www.oercommons.org/courses/linear-algebra-4
[5]: https://www.oercommons.org/hubs/open-textbooks
[8]: http://web.archive.org/web/20150604201220/https://www.boundless.com/calculus/textbooks/boundless-calculus-textbook/building-blocks-of-calculus-1/limits-8/infinite-limits-41-2926/

Geographic and historical knowledge in particular is sort of endless.
Tenochtitlán is Mexico City today, with 8.8 million people, 0.11% of
the world’s population; Mexico City’s Wikipedia page is 213kB, 33000
words; the destruction of Tenochtitlán (what is referenced in
Pandemic Collapse) is mentioned briefly after 9% of the page.  If
you divided the world into, say, 2048 regions of equal population (4
million or so), and included 4096 words or so on each of these
regions, you’d probably cover most of the geographic facts of
importance comparable to the ruin of Tenochtitlán, in about 8.3
million words, about 30,000 pages; you could read it all, once, in
three to six months.

Vital Articles
--------------

Wikipedia’s “Vital Articles” constitutes an attempt to codify such a
general-purpose body of knowledge.  There are ten Level 1 Vital
Articles, including “Human History” (21000 words, 137kB, mentions
Mexico and the Aztecs, and has a couple of sentences on the European
conquest of the Americas); 100 Level 2 Vital Articles, including 10
articles on history (the “[early modern period][0]” article has a
couple of sentences on the European conquest out of 18000 words and
120kB and mentions the Aztecs, and so does “[civilization][1]”) and 11
on geography (the “[North America][2]” article's 18000 words in 123kB
does explain, “The Mayan culture was still present in southern Mexico
and Guatemala when the Spanish conquistadors arrived, but political
dominance in the area had shifted to the Aztec Empire, whose capital
city Tenochtitlan was located further north in the Valley of
Mexico. The Aztecs were conquered in 1521 by Hernán Cortés.”); and 999
Level 3 Vital Articles, including 80 on history and 99 on geography.

[0]: https://en.wikipedia.org/wiki/Early_modern_period
[1]: https://en.wikipedia.org/wiki/Civilization
[2]: https://en.wikipedia.org/wiki/North_America

How about the killing fields of Cambodia under the Khmer Rouge, also
mentioned in the same note?  Among the Level 2 Vital Articles we find
“[Late Modern Period][3]” (19000 words, 123kB) which mentions the
Cambodian genocide, but no more; and “[Asia][4]” (15000 words, 104kB)
which mentions “the Cambodian Killing Fields”, but no more.  We don’t
find enough detail to understand the allusions in file
`pandemic-collapse.md` until Level 3, which sketches the history of
the Khmer Rouge in Cambodia in its articles “Vietnam”, “Cold War”
(36000 words, 233kB) including multiple paragraphs and a photo of a
shelf full of skulls, “Mao Zedong”, “Theravada”, “Dictatorship”, and
especially “Genocide” (17000 words, 109kB).

[3]: https://en.wikipedia.org/wiki/Late_Modern_Period

So we can infer that probably, at least when it comes to understanding
my historical references, having read all of Wikipedia’s Level 3 Vital
Articles are probably sufficient.  This is not true for scientific
knowledge; “Temperature”, “Fire”, “Electric light”, and
“Electromagnetic radiation” mention black body radiation briefly but
do not mention the Stefan–Boltzmann law.

Unfortunately the Level 3 Vital Articles are some 20 million words and
would blow out the 20-megabyte download budget for Derctuo, even
without any pictures.  The thought above of having about 4096 words
for every 4 million people would be more than adequate for Cambodia,
though, since in the 16384 words on Cambodia, we could surely find
space to mention the Khmer Rouge.

Reading Level 3 might take a year at a reasonable level of reading
speed, a bit over 1000 hours if you read it like a novel.

The OERCommons textbooks mentioned earlier include 17 history
textbooks, but most are too specific to include either of the events I
was using as test points above.  [World Civilizations I][5] (CC-BY)
was the only one that seemed broad enough to mention Cambodia, but
unfortunately has been lost.  [Western Civilization: A Concise
History, Volume 3][6] (CC-BY-NC, 105k words, 10MB as .odt, 274 pp.)
starts with Napoleon, too late to cover Cortés, but its [volume 2][7]
(CC-BY-NC, 87k words, 229 pp.) does devote a few paragraphs to the
events.

[5]: https://www.oercommons.org/courses/world-civilizations-i-open-course/view
[6]: https://www.oercommons.org/courses/western-civilization-a-concise-history-volume-3?__hub_id=19
[7]: https://www.oercommons.org/courses/western-civilization-a-concise-history-volume-2/view
