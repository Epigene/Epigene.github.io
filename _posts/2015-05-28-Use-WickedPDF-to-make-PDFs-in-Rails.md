---
layout: post
title: Use WickedPDF to make PDFs in Rails
---
##### Setup

###### 1 Add these two gems to the gemfile

  ```ruby
  # PDFs
  gem 'wicked_pdf'
  gem 'wkhtmltopdf-installer', github: 'vovayartsev/wkhtmltopdf-installer-ruby', branch: 'master'
  ```

###### 2 Make an init file and load the `wkhtmltopdf` binary path

  ```ruby
  require 'wkhtmltopdf_installer'
  WickedPdf.config = { exe_path: WkhtmltopdfInstaller.wkhtmltopdf_path }
  ```

##### Usage

###### 1 Make a controller

  ```ruby
  class PdfController < ApplicationController

    # /pdf.pdf
    def index
      respond_to do |format|
        @pdfs = PDF.all
        format.html

        format.pdf do
          render pdf: "sample_file",
                 orientation: 'Portrait',
                 page_size: 'A4',
                 layout: false,
                 disable_javascript: true,
                 grayscale: true,
                 lowquality: true,
                 no_background: true
        end

      end
    end

  end
  ```

###### 2 Make views that will be PDF friendly

  ```ruby
  # views/pdf/index.slim
  doctype html
  html
    head
      meta charset='utf-8'
    body
      - @pdfs.each |pdf|
        = pdf.som_attribute
  ```

###### 3 Read more

Options you can pass WickedPDF are in their [readme](https://github.com/mileszs/wicked_pdf)

Inserting page breaks is discussed [here](http://stackoverflow.com/questions/5806943/rails-wickedpdf-page-breaks)

Using custom fonts [here](https://shellycloud.com/blog/2014/01/generating-pdfs-with-custom-fonts-using-wkhtmltopdf)

![pdf.png]({{ site.baseurl }}/images/pdf.png)
