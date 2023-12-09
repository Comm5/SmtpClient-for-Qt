SMTP Client for Qt (C++) - Version 2.0 ![Linux Build](https://github.com/Comm5/SmtpClient-for-Qt/actions/workflows/linux-build.yml/badge.svg)
=============================================

The SmtpClient for Qt is small library written for Qt 5 / 6 (C++ version) that allows application to send complex emails (plain text, html, attachments, inline files, etc.) using the Simple Mail Transfer Protocol (SMTP).

## New in version 2.0:
- Asynchronous & Synchronous working mode

- Qt5/6 compatibility

- Building as a shared library

- code of SmtpClient refactored and partially rewrited

## New in version 1.1:

SMTP Client for Qt (C++) - Version 2.0
=============================================

The SmtpClient for Qt is small library writen for Qt 4 (C++ version) that allows application to send complex emails (plain text, html, attachments, inline files, etc.) using the Simple Mail Transfer Protocol (SMTP). This library was originally written by [Tőkés Attila!](https://github.com/bluetiger9/SmtpClient-for-Qt) but since it didn't see activity and pull requests went unanswered for years, in 2020 we decided to Fork and maintain a new version.

## New in version 2.0:
* Asynchronous & Synchronous working mode
* Qt5 compatibility
* Building as a shared library
* code of SmtpClient refactored and partially rewrited
* CMake build support

## SMTP Client for Qt supports

- TCP, SSL and TLS connections to SMTP servers
- SMTP authentication (PLAIN and LOGIN methods)
- sending MIME emails (to multiple recipients)
- multiple types of recipients (to, cc, bcc)
- plain text and HTML (with inline files) content in emails
- multiple attachments and inline files (used in HTML)
- nested mime emails (mixed/alternative, mixed/related)
- different character sets (ascii, utf-8, etc) and encoding methods (7bit, 8bit, base64)
- error handling
- output compilant with RFC2045

## Examples

Lets see a simple example:

```c++
#include <QtCore>
#include "../src/SmtpMime"

int main(int argc, char *argv[])
{
{
    QCoreApplication a(argc, argv);

    // This is a first demo application of the SmtpClient for Qt project

    // Now we create a MimeMessage object. This is the email.

    MimeMessage message;

    EmailAddress sender("your_email_address@host.com", "Your Name");
    message.setSender(sender);

    EmailAddress to("recipient@host.com", "Recipient's Name");
    message.addRecipient(to);

    message.setSubject("SmtpClient for Qt - Demo");

    // Now add some text to the email.
    // First we create a MimeText object.

    MimeText text;

    text.setText("Hi,\nThis is a simple email message.\n");

    // Now add it to the mail

    message.addPart(&text);

    // Now we can send the mail
    SmtpClient smtp("smtp.gmail.com", 465, SmtpClient::SslConnection);

    smtp.connectToHost();
    if (!smtp.waitForReadyConnected()) {
        qDebug() << "Failed to connect to host!";
        return -1;
    }

    smtp.login("your_email_address@host.com", "your_password");
    if (!smtp.waitForAuthenticated()) {
        qDebug() << "Failed to login!";
        return -2;
    }

    smtp.sendMail(message);
    if (!smtp.waitForMailSent()) {
        qDebug() << "Failed to send mail!";
        return -3;
    }

    smtp.quit();

}
```

For more examples see the [Wiki/Examples](https://github.com/bluetiger9/SmtpClient-for-Qt/wiki/Examples).

## License

This project (all files including the demos/examples) is licensed under the GNU LGPL, version 2.1.


**Copyright (c) 2020 - Comm5 Tecnologia**
**Copyright (c) 2014-2022 - Attila Tőkés**
