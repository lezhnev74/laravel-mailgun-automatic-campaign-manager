# laravel-mailgun-automatic-campaign-manager*
Manager to set automatic email campaigns, track delivery, clicks, opens etc.
*This is my handy tool I use in some of my projects.

## Purpose
It lets you programatically describe email campaigns, manage who will receive messages, set delivery time, 
timeouts between each email in a sequence.

As a programmer I can describe in Code my campaigns, adjust settings, emails, select receivers and automate all those things.
Also Mailgun will update the status of each of my emails and I can always say which emails were delivered, opened, clicked etc.

## Mailgun
The whole project is using Mailgun API to send emails, and track events for each of emails.
On sending it saves a unique message_id to DB and then receives events from Mailgun and compares them against DB to track each message events.

## Email campaigns
Email campaign is a sequence of emails sent to selected users. THere can be just one email in a campaign (like "Reset password") or 
a few messages like "educational content for new users".

How to establish a new campaign:

- add a new class (extends base class)
- set which messages are within this campaign
- set who will recieve those
- add campaign class to enabled campaign's list 

## Example
When you describe a campaign - you extend a base class and set a few functions:
```
<?php

...

class SendSequenceTestCampaign extends SendSequence
{

	/**
	 * Create a new job instance.
	 *
	 * @param $campaign_code
	 */
	public function __construct()
	{
		//
		// I set campaign code here
        //
        
		$campaign_code = "test_camp_seq_06"; // unique campaign code
		$delivery_time = "12:30"; // delivery time ex: "12:00" (24h format)

		parent::__construct($campaign_code, $delivery_time);

        //
		// here I set the sequence of emails to sent
        //
        
		$this->addToSequence(
			"letter_01", // unique code of the letter within this campaign
			"The is the subject of the first email in a sequence", // subject
			"email/html/email_campaign/test", // template html
			"email/html/email_campaign/test", // template text
			['link_to_homepage' => url('/')], // shared data passed to email's view
		);

		$this->addToSequence(
			"letter_02",
			"This is the second letter in a sequence",
			"email/html/email_campaign/test2",
			"email/html/email_campaign/test2",
			['verificationLink' => url('/aaa')],
			"+48 hours" // how much to wait after previous email delivery to send this one
		);


		// who will recieve this email?

		$this->selectUsers();

	}


	/**
	 * Select which users to notify
	 */
	protected function selectUsers() {

        //
		// Here I can implement my business logic, for example send this campaign only to users with "Paid" status
		//

		$this->users = User::paid()->get();

	}


	/**
	 * This method let me set custom user's data which is passed to an email's view
	 * 
	 * @param $user
	 * @param $sequence_code
	 */
	protected function get_custom_data($user, $sequence_code)
	{
		return [
			"name" => "prepared name for ".$user->id
		];
	}


}

```



## Installation
This library comes as a Laravel 5+ package

  
