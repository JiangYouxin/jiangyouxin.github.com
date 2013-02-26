---
layout: post
title: Linus关于git为什么不引入weave merge的帖子
meta: 转帖
---

On Sun, 23 Oct 2005, Junio C Hamano wrote:
> 
> Even if we did that, we are still doing 3-way merge; git-merge
> framework may not mesh very well when we want to use something
> like codeville merge which is not based on 3-way.

Oh, the git merge is about a million times better than any silly weave 
merge with extra BonusPoints and MagicCapitalizedNames.

Why? Because if you want to be slow and careful, you can always just 
create the weave after-the-fact and do a weave merge.

And because well-behaved git merges as so fast, you can actually afford 
to so so.

There's nothing magic in a weave merge. It's just a trick. It doesn't need 
the files to be in weave format beforehand, even though people seem to 
believe that file formats go together with it.

If somebody thinks a weave merge is wonderful and fixes everything, I have 
to rain on their parade. You still need to manually fix real conflicts up, 
and regardless, what kind of merge you do has _nothing_ to do with how you 
maintain your files.

If you want to do a weave merge inside git, then the way to do that is to 
just create the weave on demand in the (rare) case where it's needed. We 
have all the history. You might even just do a "lazy weave", which just 
starts from the common parent, and ignores the history before that. 

Much cheaper that way, and arguably nicer (others will argue that you want 
to take history into account, to decide about undo's etc. It's a matter of 
taste). 

The thing is, automatic merging isn't all _that_ important. The thing that 
made BK wonderful at merging was that it had a wonderful tool for merging 
for when there were real clashes, which is where the _really_ nasty cases 
are. The actual automatic merge wasn't necessarily anything magical.

(Same went for applying diffs, btw. What made BK nice was "renametool". Of 
course, it was also what made me decide that tracking renames was the 
wrong thing to do in the first place, but if you make a CMS that does 
renames, you'd better have a "renametool").

And if you have a tool that helps you visually merge the _real_ clashes, 
it doesn't much matter if you are only half-way decent on the automatic 
ones. They'll be so trivial that nobody cares.

And it doesn't matter _how_ good your automatic merges are, there always 
_will_ be real clashes.

[ Side note. Think about this for a while. Git did three-way merges 
  pretty much since day one, but they only became _useful_ when we made it 
  easy to see the merge conflicts and fix them up. That's a fundamental
  lesson right there: you don't have to be perfect, you have to make it 
  easy for the user to fix up your imperfections. ]

So we should spend time on making it easy to see what the clash was, and 
on tools to help resolve them. Some random merge-strategy-of-the-day is 
just bling-bling. 

The reason people like merge strategies is that it's a nice area for some 
mental masturbation. You can create all these fancy examples. And then can 
ignore the fact that most real merge problems end up being two people 
changing the same code in different ways, that just need manual merging.

Don't get me wrong - if somebody does a nice automated merge for git, it's 
a good thing, but it's probably much more important to try to integrate 
something like xxdiff to a git workflow. And _that_ level is probably 
where you want to have special language-based coloring etc to further help 
things out.

So keep your eyes on the ball. And "automatic merge" isn't it.

Linus
