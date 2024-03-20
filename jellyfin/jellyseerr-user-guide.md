---
title: Jellyseerr User Guide
description: 
published: 1
date: 2024-03-20T05:47:46.782Z
tags: 
editor: markdown
dateCreated: 2024-03-20T05:47:35.931Z
---

# Jellyseer User Guide

#
# 
#

> **Jellyseerr** is a free and open-source application to manage the tracking of media requests.
This allows you to:
> 
> - request any media (movies or tv shows)
> - search for new (hopefully interesting) content
> - view pending (and completed) requests
> - report issues with content

All of this gets funneled to me via push notification to take action (i.e. investigating streaming errors). This will simplify management of the media server as the content grows.

# Getting Started

## You will sign in with the same credentials you use on Jellyfin.

![login-screen.png](/jellyseer_user_guide/login-screen.png)Main login screen.

## This is the main screen.

![main-screen.png](/jellyseer_user_guide/main-screen.png)
Content with a green checkmark are available in Jellyfin. 
Content with a white line in a green circle are partially available in Jellyfin.

### Below are examples of content with available and partially available statuses.

![dexters-lab.png](/jellyseer_user_guide/dexters-lab.png)A TV show that is partially available. Season 1 is ***fully** available*, while Seasons 2-4 are ***partially** available*.

![napoleon.png](/jellyseer_user_guide/napoleon.png)A movie that is ***fully** available*.

## Searching for content.

You can navigate to any of the following screens to search for content: *Discover*, *Movies*, *Series*.

- The application allows you to apply various filters to find new content.

![search-filters.png](/jellyseer_user_guide/search-filters.png)View of the search filters.
Some options include: *Studio*, *Streaming Service*, *Keywords,* etc.

## Requesting content.

1. Click the *Request* button on the right-side.

![cow-n-chicken.png](/jellyseer_user_guide/cow-n-chicken.png)Main page of a TV show that is unavailable. Notice the *Request* option to the right.

1. Select the seasons you would like.
    
    ![request-tv.png](/jellyseer_user_guide/request-tv.png)
    
    Series request selection screen.
    
    ![pending-request-series.png](/jellyseer_user_guide/pending-request-series.png)
    
    Pending request of TV show.



    The same process applies to requesting movies.
    
    ![lift-new.png](/jellyseer_user_guide/lift-new.png)
    
    A movie before and after request. 
    
    1. You can view your pending/completed media requests.
    
    ![requests-page.png](/jellyseer_user_guide/requests-page.png)
    
    View of the **pending** requests on the *Requests* page.
    
    ![request-approved.png](/jellyseer_user_guide/request-approved.png)
    
    View of an **available** request on the *Requests* page.
    
    ## Reporting Issues.
    
    1. Navigate to the content that was giving issue on Jellyfin.
    
    ![napoleon-issue.png](/jellyseer_user_guide/napoleon-issue.png)
    
    View of a movie. Notice the yellow-hazard symbol on the right-side.
    
    1. Select the hazard symbol on the right-hand side.
    2. Choose the type of issue that you would like to report. Also include a brief description of the issue.
        - TV shows will allow you to select the seasons you were having issues with. If there was a particular episode you would like to mention, include it in the comment box.
        
        ![report-issue.png](/jellyseer_user_guide/report-issue.png)
        
        View of the Issue Reporting screen.