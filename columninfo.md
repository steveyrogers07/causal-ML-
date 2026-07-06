1. Pre-Treatment Variables (Context/Features)


These variables tell you about the customer before they were potentially exposed to an email campaign. You use these to build your model.

recency: Months since the customer's last purchase.

history_segment: A categorical grouping of total dollars spent by the customer in the past year.

history: The actual dollar value the customer spent in the past year.

mens: Binary (1/0). Did the customer purchase mens merchandise in the past year?

womens: Binary (1/0). Did the customer purchase womens merchandise in the past year?

zip_code: Categorical classification of the customer's location (Urban, Suburban, or Rural).

newbie: Binary (1/0). Is this a new customer acquired within the last 12 months?

channel: The channel through which the customer most frequently purchased (e.g., Phone, Web, Multichannel).




2. Treatment Variable



This is the "intervention."

segment: Which group the customer was randomly assigned to:

Mens E-Mail

Womens E-Mail

No E-Mail (This is your control group)




3. Outcome Variables (Targets)


These are the metrics you observe after the two-week experiment period. These tell you if the intervention worked.

visit: Binary (1/0). Did the customer visit the website during the two weeks following the email?

conversion: Binary (1/0). Did the customer make a purchase during the two weeks following the email?

spend: Continuous. The actual dollar amount spent by the customer during those two weeks.