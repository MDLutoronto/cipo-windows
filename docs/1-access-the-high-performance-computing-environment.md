---
title: Accessing the High Performance Computing Environment
parent: Getting Started with the Canadian Intellectual Property Office (CIPO) Patent PostgreSQL Database - Windows
layout: default
nav_order: 1
staff:
    - name: Kara Handren
      link: https://library.utoronto.ca/staff/kara-handren 
maintainer:
    - name: Leslie Barnes
      link: https://library.utoronto.ca/staff/leslie-barnes
created_date: 2023-01-05
---

## Accessing the High Performance Computing Environment

If working in high performance computing environment is new to you, we would recommend you attend [SciNet workshops](https://education.scinet.utoronto.ca/) to learn more, especially their Intro to SciNet & Triullium workshop (run periodically) or watch [a recording of a previous session](https://www.youtube.com/@scinethpcattheuniversityof8962).

  
But here are some steps to get your started:

1. To access the environment from a Windows machine, you will need an SSH client. We would recommend [MobaXterm](https://mobaxterm.mobatek.net/), and we will be using it in our tutorial examples
2. Once you have installed MobaXterm, start it up
3. From the Session menu, select New Session
4. Select SSH from the top left
5. For the remote host, use this format <computecanadausername>@trillium.scinet.utoronto.ca, substituting in your Compute Canada account username. For example, [doej@trillium.scinet.utoronto.ca](mailto:doej@trillium.scinet.utoronto.ca)
6. Click on the Advanced SSH settings tab below
7. For SSH-browser type, select SCP (enhanced speed)
8. Put a checkmark next to Use private key. Click on the blue page icon to browse to the private key you setup when creating your public key for your Compute Canada account
9. Then click on OK to connect
10. Enter in your Compute Canada account password
11. You are now connected to the server
12. To log out, type `exit` and press Enter. Then press Enter again to close the tab

**Technique:** [Searching for maps and data](https://mdlutoronto.github.io/tutorials-search/?technique=Searching+for+maps+and+data), [Text and Data Mining](https://mdlutoronto.github.io/tutorials-search/?technique=Text+and+Data+Mining) \| **Tools:** [CIPO](https://mdlutoronto.github.io/tutorials-search/?tool=CIPO)
