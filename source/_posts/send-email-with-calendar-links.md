---
title: send-email-with-calendar-links
date: 2021-09-08 18:29:33
tags:
---

## Take the note when sending an email with a calendar link
### What's the calendar link
- There was a link if you click can open user's calendar and add the event into.
- most calendar apps have their style. but usually, it's static.
### What's the .ics file
- It's a normal calendar file. it supports most calendar apps. 
- If you export from Google calendar it's a `.ics` file. You can use this file to share with other people about your calendar.
### How to implement it.
- First install the `calendar-link` package  [calendar-link](https://www.npmjs.com/package/calendar-link)
```sh
    npm i -S calendar-link
```
- Then run it on : [run kit](https://npm.runkit.com/calendar-link)
- set with the event:
```ts
// Usage with TypeScript or ES6
import { google, outlook, office365, yahoo, ics } from "calendar-link";

const event = {
  title: "My birthday party",
  description: "Be there!",
  start: "2019-12-29 18:00:00 +0100",
  end: "2019-12-29 20:00:00 +0100"
};

// Then fetch the link
google(event); // https://calendar.google.com/calendar/render...
outlook(event); // https://outlook.live.com/owa/...
office365(event); // https://outlook.office.com/owa/...
yahoo(event); // https://calendar.yahoo.com/?v=60&title=...
ics(event); // standard ICS file based on https://icalendar.org
```

- Notice the `ics(event)` will generate the file content with the header.
```js
// data:text/calendar;charset=utf8,BEGIN:VCALENDAR%0AVERSION:2.0%0ABEGIN:VEVENT%0ADTSTART:20191229T170000Z%0ADTEND:20191229T180000Z%0ASUMMARY:My%20birthday%20party%0ADESCRIPTION:Be%20there!%0AEND:VEVENT%0AEND:VCALENDAR%0A
```
- It can not add in `<a>` tag. It does not work for email. But it works for `html`. So must use an attachment attach it to the email.
### How to attach the attachment into the email
- For SendGrid it is like this:
[send grid attachments](https://github.com/sendgrid/sendgrid-nodejs/blob/main/docs/use-cases/attachments.md)

<br>

```ts
// import uuid, sendgrid, calendar-link

const event = {
  title: `I'm a title`,
  description: `I'm a description`,
  start: '2021-09-08 12:00'
  end: '2021-09-08 13:00'
}

const content = decodeURIcomponent(ics(event)).toString().split(`charset=utf-8,`)[1]
// now the content is the file body. need convert it to base64.
const data = Buffer.from(content,'base64')

const msg = {
  to: 'recipient@example.org',
  from: 'sender@example.org',
  subject: 'Hello attachment',
  html: '<p>Here’s an attachment for you!</p>',
  attachments: [
    {
      content: data,
      filename: 'some-attachment.ics',
      type: 'plain/ics',
      disposition: 'attachment',
      content_id: uuid() // must be a unique id.
    },
  ],
};
```
### Source
- Notice the sendgrid must use base64. This [Encoding Base64 Strings with Node.js](https://stackabuse.com/encoding-and-decoding-base64-strings-in-node-js/) will be helpful.
- Some html email [htmlemailboilerplate](http://htmlemailboilerplate.com/)

