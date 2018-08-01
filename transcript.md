Good afternoon/evening.

Today I'm going to talk about how we made our images a little bit smarter and a lot more flexible for our small local news startup.

But first a little about me.

My name is Russell Heimlich (like the maneuver). I'm the lead developer at Spirited Media, a local news start-up. We have sites in Philadelphia, Pittsburgh, and Denver. 

We might be a small startup comprised of a few small teams but we upload a lot of media to our WordPress-powered sites. Our sites are run on Amazon Web Services and we have 134 GB of media stored in S3. Everyday each site posts multiple articles each with at least one image. 

The Problem.

We undertook a redesign not too long ago. Our layout for how we presented our stories needed to change. Image sizes to fit these new layouts needed to change as well. 

A dreadful thing to hear as a performance-conscious WordPress developer is "we need to add a new image size". Because the way WordPress handles image resizing is by creating new, smaller versions of the image when it is first uploaded. If you tweak those sizes you need to go back and regenerate all of the new sizes again. There are plugins for that. There are command line tools to handle it for you too. It's probably manageable for small media libraries. For our media library it took 3 straight days of resizing and crunching. And the problem was only going to get worse over time. 

Now you might be wondering, "What's the big deal with just using a bigger image than needed?". Speed. According to HTTPArchive.org, the median webpage size has been creeping up and up and up over the past 8 years. The median weight of desktop webpages in the archive is 1.5 MB. A majority of that page weight comes from images. Serving a properly sized image for a given screen size takes less bandwidth, downloads in less time, results in a faster page load and ultimately makes for happier readers. You wouldn't want to wait for a 4000px wide image to download on your 480px wide device over 3G, would you?

The Solution.

We needed a way to dynamically resize images as they are needed. There are paid-services like [cloudinary.com](https://cloudinary.com/) or [imgix.com](https://www.imgix.com/) that can handle this need for us. But their expensive and we don't want to depend on a 3rd-party that could suddenly go belly-up or change their terms of service. Automattic's JetPack mega-plugin offers their [Image CDN](https://jetpack.com/support/image-cdn/) feature for free. But again we weren't too crazy about relying so heavily on a 3rd party service we didn't control. 

We decided to look at doing it ourselves. Fortunately, [Human Made had faced a similiar problem](https://humanmade.com/2017/04/27/scaling-wordpress-images-tachyon/) resulting in their open source project, [Tachyon](https://engineering.hmn.md/projects/tachyon/). Running on their own infrastrucutre that they controlled they came up with a way for dynamically resizing images via a query string in the URL. They even have a plugin to change the media URLs to use the new format and take advantage of dynamic image resizing.

We got Tachyon up and running but ran in to issues that I'll gloss over for this short talk. But Tachyon did inspire us to create our own fork called Tachyon@Edge. Here is how it works.

when a request reaches Amazon's CDN service, CloudFront, we can intercept the request and make decisions about how that request will be fulfilled. Using the serverless Lambda@Edge service, we can write a script that can fetch the original image being requested from Amazon S3. We can then resize, transform, or otherwise manipulate that image, cache a version of it for future uses, or even bypass the script entirely for non-image requests (like videos or MP3s).

What that means is you could take an image like https://a.spirited.media/wp-content/uploads/sites/3/2016/10/russell-square-pref.jpg and change it to this https://a.spirited.media/wp-content/uploads/sites/3/2016/10/russell-square-pref.jpg?resize=300,600&flop=1&flip=1&negative=1 just by adjusting a parameters in the URL. 

Experimenting with different image sizes has never been easier. No more hours and hours of recrunching. Different images are just a simple configuration change and our infrastructre handles the rest. Using `sizes` and `srcset` is a breeze to deliver the best sized image for a given screen since a range of image sizes are now available. 

Since we launched our new infrastructure we did a migration of Denverite.com Making sure their images were the right size required uploading their media to our S3 bucket and loading their site. The load time numbers speak for themselves. 

And I'm proud to announce today that we have open sourced Tachyon@Edge so other publishers can take advantage of having a dynamic image infrastructure, eliminate batch resizing of images, and speed up their load times for a better open web. 

Thank you! You can tweet me @kingkool68

Any questions?




