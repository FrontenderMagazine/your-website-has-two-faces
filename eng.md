# Your Website has Two Faces

Like the Roman god [Janus][1] (and many a politician), every web application has 
two faces: Its human face interacts with people, while its machine face 
interacts with computer systems, often as a result of those human interactions. 
Showing too much of either face to the wrong audience creates opportunity for 
error.

When a user interface—intended for human consumption—reflects too much of a 
system’s internals in its design and language, it’s likely to confuse the people 
who use it. But at the same time, if data doesn’t conform to a specific 
structure, it’s likely to confuse the machines that need to use it—so we can’t 
ignore system requirements, either.

People and machines parse information in fundamentally different ways. We need 
to find a way to balance the needs of both.

## Enter the Robustness Principle

In 1980, computer scientist Jon Postel published an [early specification][2] for 
the Transmission Control Protocol, which remains the fundamental communication 
mechanism of the internet. In this spec, he gave us the Robustness Principle:

> Be conservative in what you do, be liberal in what you accept from others.

Although often applied to low-level technical protocols like TCP, this golden 
rule of computing has broad application in the field of user experience as well.

To create a positive experience, we need to give applications a human face 
that’s liberal: empathetic, flexible, and tolerant of any number of actions the 
user might take. But for a system to be truly robust, its machine face must also 
take great care with the data it handles— treating user input as [malicious by 
default][3], and validating the format of everything it sends to downstream 
systems.

Building a system that embraces these radically different sets of constraints is 
not easy. At a high level, we might say that a *robust web application* is one 
that:

1. Accepts input from users in a variety of forms, based first on the needs and 
preferences of humans rather than machines.
2. Accepts responsibility for translating that human input to meet the 
requirements of computer systems.
3. Defines boundaries for what input is reasonable in a given context.
4. Provides clear feedback to the user, especially when the translated input 
exceeds the defined boundaries.

Whether it’s a simple form or a sophisticated application, anytime we ask users 
for input, their expectations are almost certainly different from the computer’s 
in some way. Our brains are not made of silicon. But thinking in terms of the 
Robustness Principle can help us bridge the gap between human and machine in a
wide range of circumstances.

## Numbers

Humans understand the terms “one,” “1,” and “1.00” to be roughly equivalent. 
They are very different to a computer, however. In most programming languages, 
each is a different [type][4] of data with unique characteristics. Trying to 
perform math on the wrong kind of data could lead to unexpected results. So if a 
web application needs the user to enter a number, its developers want to be sure 
that input meets the computer’s definition. Our users don’t care about such 
subtleties, but they can easily bubble up into our user interfaces.

When you buy something over the phone, the person taking your order never has to 
say, “Please give me your credit card number using only digits, with no spaces 
or dashes.” She is not confused if you pause while speaking or include a few 
“umms.” She knows a number when she hears one. But such prompts commonly litter 
web forms, instructing users to cater to the computer’s needs. Wouldn’t it be 
nice if the computer could cater to the person’s needs instead?

It often can, if we put the Robustness Principle to work to help our application 
take a variety of user input and turn it into something that meets the needs of 
a machine.

For example, we could do this right at the interface level by modifying fields 
to pre-process the user’s input, providing immediate feedback to the user about 
what’s happening. Consider an input field that’s looking for a currency value:

![Form input requesting a currency value](img/01-currency-input.png?raw=true&repo=Your Website has Two Faces "Form input requesting a currency value")

[HTML 5 introduces][5] some new attributes for the `input` element, including a 
`type` of `number` and a `pattern` attribute, intended to give developers a way 
to define the expected format of information. Unfortunately, browser support for 
these attributes remains [limited][6] and inconsistent. But a bit of JavaScript 
can do the same work. For example:

	<input onkeyup="value=value.replace(/[^0-9\.]/g,'')" />
	<input onblur="if(value.match(/[^0-9\.]/)) raise_alert(this)" />

The first input simply blocks any characters that are not digits or decimal 
points from being entered by the user. The second triggers a notification 
instead.

We can make these simple examples far more [sophisticated][7], but such 
techniques still place the computer’s rules in the user’s way. An alternative 
might be to silently accept anything the user chooses to provide, and then use 
the same regular expressions1 to process it on the server into a decimal value. 
Following guideline number three, the application would perform a sanity check 
on the result and report an error if a user entered something incomprehensible 
or out of the expected range.

Our application’s liberal human face will assume that these events are the 
exception: If we’ve designed and labeled our interfaces well, most people will 
provide reasonable input most of the time. Although precisely what people enter 
(“$10.00” or “10”) may vary, the computer can easily process the majority of 
those entries to derive the decimal value it needs, whether inline or 
server-side. But its cautious, machine-oriented face will check that assumption 
before it takes any action. If the transaction is important, like when a user 
enters the amount of a donation, the system will need to provide clear feedback 
and ask for confirmation before proceeding, even if the value falls within the 
boundaries of normalcy. Otherwise, aggressive reduction of text to a number 
could result in an unexpected—and potentially very problematic—result for our 
user:

![Overly aggressive reduction of text input to a number leads to unexpected results](img/02-donation.png?raw=true&repo=Your Website has Two Faces "Overly aggressive reduction of text input to a number leads to unexpected results")

## Dates

To a computer, dates and times are just a special case of numbers. In UNIX-based 
systems, for example, time is often represented as the number of [seconds that 
have elapsed][8] since January 1, 1970.

For a person, however, context is key to interpreting dates. When Alice asks, 
“Can we meet on Thursday?” Bob can safely assume that she means the next 
Thursday on the calendar, and he certainly doesn’t have to ask if she means 
Thursday of last week. Interface designers should attempt to get as close to 
this human experience as possible by considering the context in which a date is 
required.

We can do that by revisiting some typical methods of requesting a date from 
users:

* A text input, often with specific formatting requirements (MM/DD/YYYY, for 
example)
* A miniature calendar widget, arranging dates in a month-by-month grid

These patterns are not mutually exclusive, and a robust application might offer 
either or both, depending on the context.

There are cases where the calendar widget may be very helpful, such as 
identifying a future date that’s not known (choosing the second Tuesday of next 
February). But much of the time, a text input probably offers the fastest path 
to entering a known date, especially if it’s in the near future. If Bob wants to 
make a note about Thursday’s meeting, it seems more efficient for him to type 
the word “Thursday” or even the abbreviation “Thu” than to invoke a calendar and 
guide his mouse (or worse, his fingertip on a touchscreen) to the appropriate 
tiny box.

But when we impose overly restrictive formatting requirements on the text, we 
undermine that advantage—if Bob has to figure out the correct numeric date, and 
type it in a very specific way, he might well need the calendar after all. Or if 
an application requests Alice’s birthdate in *MM/DD/YYYY* format, why should it 
trigger an error if she types 1/1/1970, omitting the leading zeroes? In her 
mind, it’s an easily comprehensible date.

An application embracing the Robustness Principle would accept anything from the 
user that resembles a date, again providing *feedback* to confirm her entry, but 
only reporting it as a *problem* if the interpretation fails or falls out of 
bounds. A number of [software][9] [libraries][10] [exist][11] to help computers 
translate human descriptions of dates like “tomorrow,” “next Friday,” or “11 
April” into their structured, machine-oriented equivalents. Although many are 
quite sophisticated, they do have limitations—so when using them, it’s also 
helpful to provide users with examples of the most reliable patterns, even 
though the system can accept other forms of input.

## Addresses

Perhaps more often than any other type of input, address fields tend to be based 
on database design rather than the convenience of human users. Consider this 
common layout:

![Typical set of inputs for capturing an address](img/03-address.png?raw=true&repo=Your Website has Two Faces "Typical set of inputs for capturing an address")

This set of fields may cover the majority of cases for U.S. addresses, but it 
doesn’t begin to scratch the surface for [international][12] users. And even in 
the U.S., there are legitimate addresses it won’t accommodate well.

An application that wants to accept human input liberally might take the daring 
approach of using a single `textarea` to capture the address, allowing the user 
to structure it just as he or she would when composing a letter. And if the 
address will only ever be used in its entirety, storing it as a single block of 
text may be all that’s required. It’s worth asking what level of detail is truly 
needed to make use of the data.

Often we have a clear business need to store the information in discrete fields, 
however. There are many web-based and local services that can take a variety of 
address formats and standardize them, whether they were collected through a 
single input or a minimal set of structured elements.

Consider the following address:

*Avenue Appia 20*

*1211 Genève 27*

*SUISSE*

The [Google Geocoding API][13], for example, might translate it to something 
like the following, with a high level of granularity for mapping applications:

    "address_components" : [
      {
         "long_name" : "20",
         "short_name" : "20",
         "types" : [ "street_number" ]
      },
      {
         "long_name" : "Avenue Appia",
         "short_name" : "Avenue Appia",
         "types" : [ "route" ]
      },
      {
         "long_name" : "Geneva",
         "short_name" : "Geneva",
         "types" : [ "locality", "political" ]
      },
      {
         "long_name" : "Genève",
         "short_name" : "Genève",
         "types" : [ "administrative_area_level_2", "political" ]
      },
      {
         "long_name" : "Geneva",
         "short_name" : "GE",
         "types" : [ "administrative_area_level_1", "political" ]
      },
      {
         "long_name" : "Switzerland",
         "short_name" : "CH",
         "types" : [ "country", "political" ]
      },
      {
         "long_name" : "1202",
         "short_name" : "1202",
         "types" : [ "postal_code" ]
      }
    ]

The details (and license terms) of such standardization systems will vary and 
may not be appropriate for all applications. Complex addresses may be a problem, 
and we’ll need to give the application an alternate way to handle them. It will 
be more work. But to achieve the best user experience, it should be the 
application’s responsibility to first try to make sense of reasonable input. 
Users aren’t likely to care whether a CRM database wants to hold their suite 
number separately from the street name.

## The exception or the rule

Parsing human language into structured data won’t always work. Under guideline 
number four, a robust system will detect and handle edge cases gracefully and 
[respectfully][14], while working to minimize their occurrence. This long tail 
of user experience shouldn’t wag the proverbial dog. In other words, if we can 
create an interface that works flawlessly in 95 percent of cases, reducing the 
time to complete tasks and showing a level of empathy that surpasses user 
expectations, it’s probably worth the effort it takes to build an extra feedback 
loop to handle the remaining five percent.

Think again about the process of placing an order over the phone, speaking to a 
human being. If she doesn’t understand something you say, she may ask you to 
clarify. Even when she does understand, she may read the the details back to you 
to confirm. Those interactions are normal and usually courteous. In fact, they 
reassure us all that the end result of the interaction will be what we expect.

She is not, however, likely to provide you with a set of rigid instructions as 
soon as she answers the phone, and then [berate][15] you for failing to meet 
some of them. And yet web applications create the equivalent interaction all the 
time (sometimes skipping past the instructions and going directly to the 
berating).

For most developers, system integrity is an understandably high priority. Better 
structure in user-supplied data means that we can handle it more reliably. We 
want reliable systems, so we become advocates for the machine’s needs. When 
input fails to pass validation, we tend to view it as a failure of the user—an 
error, an attempt to feed bad data into *our* carefully designed application.

But whether or not our [job titles][16] include the phrase “user experience,” we 
must advocate at least as much for the people who use our software as we do for 
computer systems. Whatever the problem a web application is solving, ultimately 
it was created to benefit a human being. Everyone who has a hand in building an 
application influences the experience, so improving it is everyone’s job. 
Thinking in terms of robustness can help us balance the needs of both people and 
computers.

Postel’s Law has proven its worth by running the internet for more than three 
decades. May we all hold our software—and the experiences it creates—to such a 
high standard.
 
[1]: http://en.wikipedia.org/wiki/Janus
[2]: http://tools.ietf.org/html/rfc761
[3]: https://www.owasp.org/index.php/Data_Validation#Sanitize_with_Whitelist
[4]: http://en.wikipedia.org/wiki/Data_type
[5]: http://alistapart.com/article/forward-thinking-form-validation
[6]: http://www.quirksmode.org/html5/inputs.html
[7]: www.alistapart.com/articles/inline-validation-in-web-forms/
[8]: http://www.epochconverter.com/
[9]: http://lee.jarvis.co/chronic/
[10]: http://php.net/manual/en/function.strtotime.php
[11]: http://natty.joestelmach.com/
[12]: http://www.columbia.edu/~fdc/postal/
[13]: https://developers.google.com/maps/documentation/geocoding/
[14]: http://alistapart.com/article/good-help-is-hard-to-find
[15]: http://en.wikipedia.org/wiki/The_Soup_Nazi
[16]: http://archive.aneventapart.com/alasurvey2011/00.html#jt
