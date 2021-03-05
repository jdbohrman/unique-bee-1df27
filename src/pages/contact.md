---
title: Contact
hide_title: false
sections:
  - section_id: contact-form
    content: To get in touch please fill the form below.
    form_id: contactForm
    form_action: /thank-you
    form_fields:
      - input_type: text
        name: name
        label: Name
        default_value: Your name
        is_required: true
        type: form_field
      - input_type: email
        name: email
        label: Email
        default_value: Your email address
        is_required: true
        type: form_field
      - input_type: select
        name: subject
        label: Subject
        default_value: Please select
        options:
          - Error on the site
          - Sponsorship
          - Other
        type: form_field
      - input_type: textarea
        name: message
        label: Message
        default_value: Your message
        type: form_field
      - input_type: checkbox
        name: consent
        label: >-
          I understand that this form is storing my submitted information so I
          can be contacted.
        type: form_field
    submit_label: Send Message
    type: section_form
seo:
  title: Contact
  description: This is the contact page
  extra:
    - name: 'og:type'
      value: website
      keyName: property
      type: stackbit_page_meta_extra
    - name: 'og:title'
      value: Contact
      keyName: property
      type: stackbit_page_meta_extra
    - name: 'og:description'
      value: This is the contact page
      keyName: property
      type: stackbit_page_meta_extra
    - name: 'twitter:card'
      value: summary
      type: stackbit_page_meta_extra
    - name: 'twitter:title'
      value: Contact
      type: stackbit_page_meta_extra
    - name: 'twitter:description'
      value: This is the contact page
      type: stackbit_page_meta_extra
  type: stackbit_page_meta
template: advanced
stackbit_url_path: /contact
---
