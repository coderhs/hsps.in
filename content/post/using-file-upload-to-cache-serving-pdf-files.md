---
title: "Caching Rendered PDFs in Rails with Active Storage"
date: 2025-07-26T21:47:37+05:30
lastmod: 2025-07-26T21:47:37+05:30
draft: false
keywords: ["pdf", "wicked_pdf", "cache", "active storage", "performance"]
description: "Stop Re-rendering PDFs: Cache Them with Active Storage in Rails"
tags: ["pdf", "wicked_pdf", "cache", "active storage", "performance"]
categories: ["rails"]
author: "Harisankar P S"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
toc: false
autoCollapseToc: false
postMetaInFooter: false
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false

# You unlisted posts you might want not want the header or footer to show
hideHeaderAndFooter: false

# You can enable or disable out-of-date content warning for individual post.
# Comment this out to use the global config.
#enableOutdatedInfoWarning: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams:
  enable: false
  options: ""

---

As I was working on [easyclientlog.com](https://easyclientlog.com), building its invoice system that allows freelancers/consultants to generates PDFs. Working with PDF generating is that, its takes time and CPU cycles. But most of the time
the pdf only needs to be generated once, and the content seldom changes. So rendering the same thing over and over just didn’t feel right. So I used a simple trick that I’ve followed in many of my prior Rails apps: upload the PDF on first render, store it using Active Storage, and reuse that file on the next access.

## The Idea: Cache on First Render

When the PDF is generated the first time, we attach it to the record using Active Storage. This could be an invoice, report, or any other object. The next time we need to show or download the PDF, we skip the rendering step and simply serve the uploaded file.

This avoids unnecessary rendering and speeds up response times, especially for large documents or when generating in bulk.

<!--more-->


## How It Works

Let’s say we have an `Invoice` model. We attach the PDF like this:

```ruby
class Invoice < ApplicationRecord
  has_one_attached :pdf
end
```

Now in the PDF rendering service, we check if the file already exists. If yes, we use it. If not, we generate the PDF and attach it.

```ruby
class InvoicePdfService
  include ActionView::Helpers

  def initialize(invoice)
    @invoice = invoice
  end

  def generate
    Rails.logger.info "Generating PDF for invoice #{@invoice.id}"
    Rails.logger.info "Invoice line_items: #{@invoice.line_items.inspect}"

    # If PDF already attached, return it
    if @invoice.pdf.attached?
      Rails.logger.info "Returning cached PDF for invoice #{@invoice.id}"
      return @invoice.pdf.download
    end

    html_content = render_invoice_html
    Rails.logger.info "Generated HTML length: #{html_content.length}"

    pdf_data = WickedPdf.new.pdf_from_string(
      html_content,
      pdf_options
    )
    # Attach the PDF to the invoice for future use
    @invoice.pdf.attach(
      io: StringIO.new(pdf_data),
      filename: "invoice-#{@invoice.invoice_number}.pdf",
      content_type: 'application/pdf'
    )
    pdf_data
  rescue => e
    Rails.logger.error "PDF generation failed: #{e.message}"
    Rails.logger.error e.backtrace.join("\n")
    raise e
  end

  private

  def render_invoice_html
    ApplicationController.render(
      template: 'invoices/pdf',
      layout: 'layouts/pdf',
      assigns: {
        invoice: @invoice,
        currency: @invoice.team.invoice_setting&.currency || '$'
      }
    )
  rescue => e
    Rails.logger.error "Template rendering failed: #{e.message}"
    raise e
  end

  def pdf_options
    {
      page_size: 'A4',
      margin_top: 0.75,
      margin_right: 0.75,
      margin_bottom: 0.75,
      margin_left: 0.75,
      encoding: 'UTF-8',
      orientation: 'Portrait',
      print_media_type: true,
      disable_smart_shrinking: true,
      lowquality: false,
      zoom: 1,
      dpi: 300,
      enable_local_file_access: true,
      javascript_delay: 500,
      no_stop_slow_scripts: true,
      enable_plugins: true,
      load_error_handling: 'ignore'
    }
  end
end
```


## When to Invalidate the Cache

In our case, the cache/storage is cleared by deleting the attached PDF when the invoice is updated:

```ruby
def update
  @invoice.pdf.purge if @invoice.pdf.attached?
  @invoice.update(invoice_params)
end
```

This deletes the old file. Active Storage will take care of removing it from the cloud (S3, GCS, etc.) in the background.

Alternatively, you could skip deletion and just change the filename by including `updated_at`. This way, older versions are not used, and Active Storage can expire old files on its own using lifecycle rules.


## Summary

Instead of rendering the same PDF again and again, use Active Storage to cache it. Attach the file on first render and reuse it. Delete it when updating the record, or use the `updated_at` time in the filename and compare on file download if
the pdf has changed.

This trick has helped me improve performance and simplify my code in apps where PDFs are generated regularly. Hope it helps you too.

Note: If you are adding some dynamic data to your PDF like download time, then this method can't be used.
