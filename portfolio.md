---
layout: page
title: "Portfolio"
group: corePages
description: ""
---
{% include JB/setup %}

<h2>Selected Work</h2>

<section class="portfolio-piece">
	<h4><b><i>Rolltec</i> Wearable for Martial Artists</b></h4>
	<br>
	<p><b>Description:</b> 
	<br>
	I built the prototype of this wearable for grappling martial artists between October 2015 and April 2016. It consists of:</p>

	<ol>
	<li>The hardware: the shirt and the sensors (accelerometer and gyroscope, made by the great guys at <a href="https://mbientlab.com">mbient lab)</a></li>
	<li>An Android mobile app which streams data from the sensors</li>
	<li>A Python (Flask) server which conducts machine learning classification on the data to give users insights into their training.</li>

	</ol>

	<p>

	I am no longer actively developing this project. <a href="https://github.com/ChristopherGS/rolltec_motion">I have open sourced all code and data on my github.</a>

	Unfortunately, attempts to crowdfund the project were unsuccessful. <a href="https://www.kickstarter.com/projects/1489178162/rolltec-grappling-the-first-wearable-made-for-grap/">You can see the kickstarter campaign here.</a>

	</p>

	<p>

	This project was challenging and rewarding for a number of reasons. The data science classification required a lot of exploration of scikit-learn, as well as other Python machine learning libraries such as Hidden Markov Models with <a href="https://github.com/hmmlearn/hmmlearn">hmmlearn.</a> The data processing required extensive use of pandas, as well as numpy, matplotlib and other libraries from the scientific Python stack. I also taught myself Android for the project.
	</p>

	

	<div class="row">

		<div class="col-lg-5 col-sm-12">
			<p><em>Wearable Mobile App</em></p>
			<img src="{{ ASSET_PATH }}/resources/images/app-examples2.jpg"/>
		</div>

		<div class="col-lg-2 portfolio-image"></div>

		<div class="col-lg-5 col-sm-12 portfolio-image">
			<p><em>Wearable Components</em></p>
			<img src="{{ ASSET_PATH }}/resources/images/rolltec_diagrams-04cropped.jpg"/> 
		</div>
	</div>

</section>

<section class="portfolio-piece">
	<h4><b><i>Emergency Response</i> First Aid Testing</b></h4>
	<br>
	<p><b>Description:</b> </p>
	<br>

	<p>A fully response, single page web application used to examine first aid students in Sydney.</p>
	<p><b>Technologies used: </b>Django (with djangorestframework), React</p>
	<p><b>Live URL: </b><a href="http://www.resus.net.au/login/">resus.net.au</a></p>

	<div class="row">

		<div class="col-lg-5 col-sm-12">
			<p><em>Desktop Site</em></p>
			<img src="{{ ASSET_PATH }}/resources/images/emergency_response_desktop_scr.jpg"/>
		</div>

		<div class="col-lg-2 portfolio-image"></div>

		<div class="col-lg-5 col-sm-12 portfolio-image">
			<p><em>Mobile Site</em></p>
			<img src="{{ ASSET_PATH }}/resources/images/emergency_response_mobile_scre.jpg"/> 
		</div>
	</div>

</section>

<section class="portfolio-piece">

	<h4><b>"Digital Grappling"</b></h4>

	<br>
	<p><b>Description:</b> 

	<br>

	This is a fully responsive, single page web application used by over 5000 martial artists to help them actively learn
	Brazilian Jiu-Jitsu when they are unable to train at the dojo.

	The application is a playable state diagram for the martial art, and required the creation of thousands of move tree
	schematics in order to create a realistic experience. The player is incentivised through game stats, which track their ability
	to hit certain moves and promotes them to higher virtual belt rankings accordingly.</p>

	<p><b>Technologies used: </b>Node.js (with express.js), mongodb, backbone.js, bootstrap</p>
	<p><b>My Contribution: </b>I did all development across the stack. The Jiu-Jitsu move sequences were
	created by Roger Gracie blackbelt <a href="http://jiujitsubrotherhood.com">Nic Gregoriades</a>. The design concept is by the very talented
	<a href="https://www.linkedin.com/pub/katie-mihailovits/b/40/800">Katie Mihailovits</a></p>

	<p><b>Live URL: </b><a href="http://digitalgrappling.com">digitalgrappling.com</a></p>

	<p><a href="http://www.reddit.com/r/bjj/comments/2q21p5/digital_grappling_is_here/">Here's what people say about it on reddit</a></p>

	<div class="row">

		<div class="col-lg-5 col-sm-12">
			<p><em>Desktop Site</em></p>
			<img src="{{ ASSET_PATH }}/resources/images/rsz_digital_grappling_dt.jpg"/>
		</div>

		<div class="col-lg-2 portfolio-image"></div>

		<div class="col-lg-5 col-sm-12 portfolio-image">
			<p><em>Mobile Site</em></p>
			<img src="{{ ASSET_PATH }}/resources/images/rsz_digital_grappling_mb.jpg"/> 
		</div>
	</div>

</section>

<hr>

<section class="portfolio-piece">

	<h4><b>"MMA Data Science"</b></h4>

	<br>
	<p><b>Description:</b> 

	<br>

	This is a web scraping site which displays a visualization of a given MMA fighter's career. The tool
	is able to capture and clean up the data of every listed professional MMA fighter. 


	</p>


	<p><b>Technologies used: </b>Django, <a href="http://www.crummy.com/software/BeautifulSoup/bs4/doc/">Beautiful Soup</a>, PostgreSQL, <a href="http://d3js.org/">D3.js</a></p>

	<p><b>My Contribution: </b>I was the sole developer on this project.</p>

	<p><b>Live URL: </b><a href="http://mmadatascience.com">mmadatascience.com</a></p>

	<p><b>Source Code: </b> <a href="http://github.com/christophergs/mmadatascience">Link to my github</a></p>


	<p><em>Application screenshot:</em></p>
	
	<div class="row">



		<div class="col-xs-12">
			<img src="{{ ASSET_PATH }}/resources/images/mma2.jpg"/>
		</div>


	</div>

</section>

<hr>

<section class="portfolio-piece">

	<h4><b>Bupa.co.uk: Quote and Buy Application</b></h4>
	<br>
	<p><b>Description:</b></p> 

	<p>
	Work from my time as the front-end developer in <a href="http://en.wikipedia.org/wiki/Bupa">Bupa's</a> UK web scrum. During my time there, we completely reworked the customer journey for the flagship "Bupa by You" quote and buy heath insurance application. The work we did here improved online conversions by 30%.
	</p>

	<p><b>Technologies used: </b>HTML5, JQuery</a></p>

	<p><b>My Contribution: </b>I was responsible for the front-end of the site. The backend (written in Java) was taken care of by a team of five developers, both onshore and offshore.</p>

	<p><b>Live URL: </b><a href="https://www.bupa.co.uk/individuals/health-insurance/quote-your-details">Bupa Health Insurance</a></p>


	<div class="row">

		<div class="col-lg-12 col-sm-12">
			<img src="{{ ASSET_PATH }}/resources/images/rsz_bby.jpg"/>
		</div>

	</div>

</section>

<hr>

<section class="portfolio-piece">

	<h4><b>Stripe-Taxamo subscription integrations for Node.js</b></h4>
	<br>
	<p><b>Description:</b></p> 

	<p>
	At the beginning of 2015, the EU introduced new VAT tax requirements for businesses selling digital products. The requirements are stringent and require two non-conflicting pieces of information (such as IP address and credit card details) to identify a customer's location so that the correct VAT tax can be applied to any purchases. I wrote an open source Node.js solution to help solve this problem for those who have Stripe subscription customers and are using the Taxamo platform.
	</p>

	<p><b>Technologies used: </b>Node.js (express.js), <a href="https://stripe.com">Stripe</a>, <a href="https://www.taxamo.com/">Taxamo</a></p>

	<p><b>Full write up: </b>I have written a <a href="http://thelifelifebalance.com/new-eu-vat-regulations-a-solution-with-stripe-and-taxamo/">detailed post</a> on both the problem and solution</p>

	<p><b>Source code: </b><a href="https://github.com/ChristopherGS/node_stripe_taxamo">Available here on github</a></p>


</section>
