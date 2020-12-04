The Bilara webapp offers the ability to publish texts directly from the webapp. 

**Check and double-check your work before publishing! We strongly encourage one or two rounds of proofreading by a third party. SC is not a blog or social media: you are creating sacred texts to last the test of time. Take the time to do it well. [Remember the chariot-maker!](https://suttacentral.net/an3.15/en/sujato)**

>Then the chariot-maker rolled forth the wheel that had been finished in six days. It rolled as far as the original impetus took it, then wobbled and fell down. Then he rolled forth the wheel that had been finished in six days less than six months. It rolled as far as the original impetus took it, then stood still as if fixed to an axle.

Bilara lets you publish at any level of granularity: a sutta, a vagga, a whole nikaya. If you publish a nikaya, you don't have to publish each sutta individually. These options are there to accommodate the different working preferences of translators. If you prefer to publish one sutta at a time, you can. Or if you prefer to complete and revise a whole nikaya before publishing all at once, you can.

Before publishing, check that your publication metadata is correct. This is the info that will be supplied alongside your text. Not every field need be filled in, but the more the better.

https://github.com/suttacentral/bilara-data/blob/published/_publication.json

Now we’re ready to publish.

1. When ready, click <kbd>Publish</kbd> on Bilara.
2. Confirm the publication when the dialog pops up.
    - The dialog gives you a link to your publication on Github.
3. There is now a "Pull Request" open on Github. This is awaiting review by an Admin member of the SuttaCentral team before accepting it to the "Published" branch. If there is any problem they will contact you. 
    - This Pull Request is not for proof reading or otherwise checking the text in detail. It is to ensure that the pull is technically valid and that the material seems reasonable.
4. A SuttaCentral Admin accepts the PR. Your text is now in the [Published branch of Bilara-data](https://github.com/suttacentral/bilara-data/tree/published).
    - This is the canonical home of your text. Texts in the Published branch will automatically become available for publishing by any app that consumes bilara-data. This includes the SuttaCentral website and SC-Voice. It may also include other apps that third-parties build relying on our data.

Your work is now published. Congratulations!

>*Yikes, I made a mistake!*

Never mind, to correct or update your text, just make the changes as usual in Bilara, then press "Update". 
