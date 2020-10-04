---
title: Skyee API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - Sample
  - csharp: C#
toc_footers:


includes:
  - accom
  - errors

search: true

code_clipboard: true
---

# Introduction

Welcome to the Skyee API!

This guide is intended to be used for integration with Skyee’s Accommodation/Tuition collection service via Bank Transfer.

If you intend to integrate Skyee's collection service into your website,
you will need to do the following steps:

1. Acquire a merchantId from Skyee's account manager.
2. Acquire an appId from Skyee's account manager.
3. Get a certificate key from Skyee so you can verify the message signature sent by Skyee.
4. Issue a certificate key to Skyee so Skyee can verify the message signature 
sent with your request.

With the above information ready, a general payment process would be like: