I want to snarf some of sciencemadness before it goes down, such as:

<http://www.sciencemadness.org/talk/viewthread.php?tid=1245&page=2>

That thread is in forum 2
<http://www.sciencemadness.org/talk/forumdisplay.php?fid=2>, which has
216 pages such as
<http://www.sciencemadness.org/talk/forumdisplay.php?fid=2&page=3>.
They link to pages like
<http://www.sciencemadness.org/talk/viewthread.php?tid=156102> which
may themselves have page numbers.  Sometimes they link to attachments
like
<http://www.sciencemadness.org/talk/files.php?pid=643614&aid=82955>
and include images like
<http://www.sciencemadness.org/talk/images/xpblue/default_icon.gif>.

There's the risk that a thread in that forum might link to a thread in
another forum, and then another, etc., but I think that mostly won't
happen.

So some regexps would be something like

    http://www\.sciencemadness\.org/talk/forumdisplay\.php\?fid=2(?:&page=\d+)?
    http://www\.sciencemadness\.org/talk/viewthread\.php\?tid=\d+(?:&page=\d+)?
    http://www\.sciencemadness\.org/talk/files\.php\?pid=\d+&aid=\d+
    http://www\.sciencemadness\.org/talk/images/.*

So I think the command is something like this:

    time wget -r -l inf -np --regex-type pcre -w 17 --retry-connrefused \
      --accept-regex 'http://www\.sciencemadness\.org/talk/(?:images/.*|files\.php\?pid=\d+&aid=\d+|viewthread\.php\?tid=\d+|forumdisplay\.php\?fid=2(&page=\d+))' \
      http://www.sciencemadness.org/talk/forumdisplay.php?fid=2

I can't use `-N` because the messageboard doesn't provide
Last-Modified.  `-nc` doesn't do the right thing because wget doesn't
know to reparse the on-disk forum indexes.  I have to use `-l inf`
because the default is 5.

Happily they do have a robots.txt that implicitly allows this kind of
mirroring.

The above does end up with a bunch of duplicates:

    www.sciencemadness.org/talk/viewthread.php?tid=27851&goto=search&pid=310821
    www.sciencemadness.org/talk/viewthread.php?tid=27851&goto=search&pid=310836

etc.  So far this is only a minor irritant, a tarpit that has sucked
up 21 out of the 190 files I've snarfed so far on a single thread;
wget isn't smart enough to notice that these are all just redirects to
things like

    http://www.sciencemadness.org/talk/viewthread.php?tid=27851#pid310836

This suggests that maybe the regexp is not being required to match the
whole URL, just the beginning (or maybe anywhere).  Also I hadn't
allowed the &page= on the viewthread regexp at the time, so I guess
it's pretty certain.

All right, trying again; seems to be working better now:

    time wget -r -l inf -np --regex-type pcre -w 17 --retry-connrefused \
      --accept-regex 'http://www\.sciencemadness\.org/talk/(?:images/.*|files\.php\?pid=\d+&aid=\d+|viewthread\.php\?tid=\d+(?:&page=\d+)?|forumdisplay\.php\?fid=2(?:&page=\d+)?)$' \
      http://www.sciencemadness.org/talk/forumdisplay.php?fid=2

Initially I tried it with `-w 1.7` until I was sure I'd fixed *that*
problem.  Now, half a gig later, it seems to be doing okay, though
some images have been uploaded twice.  Maybe `--page-requisites` would
be a good idea but I don't know how it interacts with
`--accept-regex`.