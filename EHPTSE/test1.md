::: {#contents .section .contents}
## Contents

[About This Report](#about-this-report)

1.  [Report Parameters](#report-parameters)

[Summaries](#summaries)

1.  [Alert Counts by Risk and Confidence](#risk-confidence-counts)
2.  [Alert Counts by Site and Risk](#site-risk-counts)
3.  [Alert Counts by Alert Type](#alert-type-counts)
4.  [Insights](#insights)

[Alerts](#alerts)

1.  [Risk=[Medium]{.risk-level}, Confidence=[High]{.confidence-level}
    (2)](#alerts--risk-2-confidence-3)
2.  [Risk=[Medium]{.risk-level}, Confidence=[Medium]{.confidence-level}
    (1)](#alerts--risk-2-confidence-2)
3.  [Risk=[Low]{.risk-level}, Confidence=[Low]{.confidence-level}
    (1)](#alerts--risk-1-confidence-1)
4.  [Risk=[Informational]{.risk-level},
    Confidence=[Medium]{.confidence-level}
    (2)](#alerts--risk-0-confidence-2)

[Appendix](#appendix)

1.  [Alert Types](#alert-types)
:::

::::: {#about-this-report .section .about-this-report}
## About This Report

:::: {#report-parameters .section}
### Report Parameters

::: report-parameters--container
#### Contexts

No contexts were selected, so all contexts were included by default.

#### Sites

The following sites were included:

- [http://172.28.43.116:3000]{.site}

(If no sites were selected, all sites were included by default.)

An included site must also be within one of the included contexts for
its data to be included in the report.

#### Risk levels

Included: [[High]{.risk-level}, [Medium]{.risk-level},
[Low]{.risk-level}, [Informational]{.risk-level}]{.included-risk-codes}

Excluded: None

#### Confidence levels

Included: [[User Confirmed]{.confidence-level},
[High]{.confidence-level}, [Medium]{.confidence-level},
[Low]{.confidence-level}]{.included-confidence-codes}

Excluded: [ [User Confirmed]{.confidence-level},
[High]{.confidence-level}, [Medium]{.confidence-level},
[Low]{.confidence-level}, [False
Positive]{.confidence-level}]{.included-confidence-codes}
:::
::::
:::::

::: section
:::

::::::: {#summaries .section .summaries}
## Summaries

::: {#risk-confidence-counts .section}
### Alert Counts by Risk and Confidence

+----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|                      | Confidence                                                                                                                                                                                                     |
|                      +----------------------------------------+-----------------------------------------+-----------------------------------------+-----------------------------------------+-----------------------------------------+
|                      | User Confirmed                         | High                                    | Medium                                  | Low                                     | Total                                   |
+======+===============+========================================+=========================================+=========================================+=========================================+=========================================+
| Risk | High          | 0\                                     | 0\                                      | 0\                                      | 0\                                      | 0\                                      |
|      |               | [(0.0%)]{.additional-info-percentages} | [(0.0%)]{.additional-info-percentages}  | [(0.0%)]{.additional-info-percentages}  | [(0.0%)]{.additional-info-percentages}  | [(0.0%)]{.additional-info-percentages}  |
|      +---------------+----------------------------------------+-----------------------------------------+-----------------------------------------+-----------------------------------------+-----------------------------------------+
|      | Medium        | 0\                                     | 2\                                      | 1\                                      | 0\                                      | 3\                                      |
|      |               | [(0.0%)]{.additional-info-percentages} | [(33.3%)]{.additional-info-percentages} | [(16.7%)]{.additional-info-percentages} | [(0.0%)]{.additional-info-percentages}  | [(50.0%)]{.additional-info-percentages} |
|      +---------------+----------------------------------------+-----------------------------------------+-----------------------------------------+-----------------------------------------+-----------------------------------------+
|      | Low           | 0\                                     | 0\                                      | 0\                                      | 1\                                      | 1\                                      |
|      |               | [(0.0%)]{.additional-info-percentages} | [(0.0%)]{.additional-info-percentages}  | [(0.0%)]{.additional-info-percentages}  | [(16.7%)]{.additional-info-percentages} | [(16.7%)]{.additional-info-percentages} |
|      +---------------+----------------------------------------+-----------------------------------------+-----------------------------------------+-----------------------------------------+-----------------------------------------+
|      | Informational | 0\                                     | 0\                                      | 2\                                      | 0\                                      | 2\                                      |
|      |               | [(0.0%)]{.additional-info-percentages} | [(0.0%)]{.additional-info-percentages}  | [(33.3%)]{.additional-info-percentages} | [(0.0%)]{.additional-info-percentages}  | [(33.3%)]{.additional-info-percentages} |
|      +---------------+----------------------------------------+-----------------------------------------+-----------------------------------------+-----------------------------------------+-----------------------------------------+
|      | Total         | 0\                                     | 2\                                      | 3\                                      | 1\                                      | 6\                                      |
|      |               | [(0.0%)]{.additional-info-percentages} | [(33.3%)]{.additional-info-percentages} | [(50.0%)]{.additional-info-percentages} | [(16.7%)]{.additional-info-percentages} | [(100%)]{.additional-info-percentages}  |
+------+---------------+----------------------------------------+-----------------------------------------+-----------------------------------------+-----------------------------------------+-----------------------------------------+

: This table shows the number of alerts for each level of risk and
confidence included in the report.\
(The percentages in brackets represent the count as a percentage of the
total number of alerts included in the report, rounded to one decimal
place.)
:::

::: {#site-risk-counts .section}
### Alert Counts by Site and Risk

+----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|                                  | Risk                                                                                                                                                                |
|                                  +--------------------------------------+----------------------------------------+-------------------------------------+-----------------------------------------------+
|                                  | High\                                | Medium\                                | Low\                                | Informational\                                |
|                                  | [(=                                  | [(\>=                                  | [(\>=                               | [(\>=                                         |
|                                  | High)]{.additional-info-percentages} | Medium)]{.additional-info-percentages} | Low)]{.additional-info-percentages} | Informational)]{.additional-info-percentages} |
+======+===========================+======================================+========================================+=====================================+===============================================+
| Site | http://172.28.43.116:3000 | 0\                                   | 3\                                     | 1\                                  | 2\                                            |
|      |                           | [(0)]{.additional-info-percentages}  | [(3)]{.additional-info-percentages}    | [(4)]{.additional-info-percentages} | [(6)]{.additional-info-percentages}           |
+------+---------------------------+--------------------------------------+----------------------------------------+-------------------------------------+-----------------------------------------------+

: This table shows, for each site for which one or more alerts were
raised, the number of alerts raised at each risk level.\
Alerts with a confidence level of \"False Positive\" have been excluded
from these counts.\
(The numbers in brackets are the number of alerts raised for the site at
or above that risk level.)
:::

::: {#alert-type-counts .section}
### Alert Counts by Alert Type

  ----------------------------------------------------------------------------------------------------
  Alert type                         Risk                    Count
  ---------------------------------- ----------------------- -----------------------------------------
  [CSP: Failure to Define Directive  Medium                  2\
  with No Fallback](#alert-type-0)                           [(33.3%)]{.additional-info-percentages}

  [Content Security Policy (CSP)     Medium                  5\
  Header Not Set](#alert-type-1)                             [(83.3%)]{.additional-info-percentages}

  [Cross-Domain                      Medium                  5\
  Misconfiguration](#alert-type-2)                           [(83.3%)]{.additional-info-percentages}

  [Timestamp Disclosure -            Low                     5\
  Unix](#alert-type-3)                                       [(83.3%)]{.additional-info-percentages}

  [Modern Web                        Informational           5\
  Application](#alert-type-4)                                [(83.3%)]{.additional-info-percentages}

  [User Agent Fuzzer](#alert-type-5) Informational           5\
                                                             [(83.3%)]{.additional-info-percentages}

  Total                                                      6
  ----------------------------------------------------------------------------------------------------

  : This table shows the number of alerts of each alert type, together
  with the alert type\'s risk level.\
  (The percentages in brackets represent each count as a percentage,
  rounded to one decimal place, of the total number of alerts included
  in this report.)
:::

::: {#insights .section}
### Insights {.left-header}

+-------------+---------------+---------------------------+--------------------------+-------------+
| Level       | Reason        | Site                      | Description              | Statistic   |
+=============+===============+===========================+==========================+:===========:+
| <div>       | <div>         | <div>                     | <div>                    | <div>       |
|             |               |                           |                          |             |
| Medium      | Exceeded Low  | </div>                    | Percentage of memory     | 84          |
|             |               |                           | used                     |             |
| </div>      | </div>        |                           |                          | </div>      |
|             |               |                           | </div>                   |             |
+-------------+---------------+---------------------------+--------------------------+-------------+
| <div>       | <div>         | <div>                     | <div>                    | <div>       |
|             |               |                           |                          |             |
| Low         | Warning       | </div>                    | ZAP errors logged - see  | 4           |
|             |               |                           | the zap.log file for     |             |
| </div>      | </div>        |                           | details                  | </div>      |
|             |               |                           |                          |             |
|             |               |                           | </div>                   |             |
+-------------+---------------+---------------------------+--------------------------+-------------+
| <div>       | <div>         | <div>                     | <div>                    | <div>       |
|             |               |                           |                          |             |
| Low         | Warning       | </div>                    | ZAP warnings logged -    | 2,657       |
|             |               |                           | see the zap.log file for |             |
| </div>      | </div>        |                           | details                  | </div>      |
|             |               |                           |                          |             |
|             |               |                           | </div>                   |             |
+-------------+---------------+---------------------------+--------------------------+-------------+
| <div>       | <div>         | <div>                     | <div>                    | <div>       |
|             |               |                           |                          |             |
| Low         | Exceeded Low  | </div>                    | Percentage of network    | 12 %        |
|             |               |                           | failures                 |             |
| </div>      | </div>        |                           |                          | </div>      |
|             |               |                           | </div>                   |             |
+-------------+---------------+---------------------------+--------------------------+-------------+
| <div>       | <div>         | <div>                     | <div>                    | <div>       |
|             |               |                           |                          |             |
| Info        | Informational | http://172.28.43.116:3000 | Percentage of responses  | 98 %        |
|             |               |                           | with status code 2xx     |             |
| </div>      | </div>        | </div>                    |                          | </div>      |
|             |               |                           | </div>                   |             |
+-------------+---------------+---------------------------+--------------------------+-------------+
| <div>       | <div>         | <div>                     | <div>                    | <div>       |
|             |               |                           |                          |             |
| Info        | Informational | http://172.28.43.116:3000 | Percentage of responses  | 1 %         |
|             |               |                           | with status code 3xx     |             |
| </div>      | </div>        | </div>                    |                          | </div>      |
|             |               |                           | </div>                   |             |
+-------------+---------------+---------------------------+--------------------------+-------------+
| <div>       | <div>         | <div>                     | <div>                    | <div>       |
|             |               |                           |                          |             |
| Info        | Informational | http://172.28.43.116:3000 | Percentage of responses  | 1 %         |
|             |               |                           | with status code 4xx     |             |
| </div>      | </div>        | </div>                    |                          | </div>      |
|             |               |                           | </div>                   |             |
+-------------+---------------+---------------------------+--------------------------+-------------+
| <div>       | <div>         | <div>                     | <div>                    | <div>       |
|             |               |                           |                          |             |
| Info        | Informational | http://172.28.43.116:3000 | Percentage of endpoints  | 4 %         |
|             |               |                           | with content type        |             |
| </div>      | </div>        | </div>                    | application/javascript   | </div>      |
|             |               |                           |                          |             |
|             |               |                           | </div>                   |             |
+-------------+---------------+---------------------------+--------------------------+-------------+
| <div>       | <div>         | <div>                     | <div>                    | <div>       |
|             |               |                           |                          |             |
| Info        | Informational | http://172.28.43.116:3000 | Percentage of endpoints  | 7 %         |
|             |               |                           | with content type        |             |
| </div>      | </div>        | </div>                    | application/octet-stream | </div>      |
|             |               |                           |                          |             |
|             |               |                           | </div>                   |             |
+-------------+---------------+---------------------------+--------------------------+-------------+
| <div>       | <div>         | <div>                     | <div>                    | <div>       |
|             |               |                           |                          |             |
| Info        | Informational | http://172.28.43.116:3000 | Percentage of endpoints  | 1 %         |
|             |               |                           | with content type        |             |
| </div>      | </div>        | </div>                    | image/x-icon             | </div>      |
|             |               |                           |                          |             |
|             |               |                           | </div>                   |             |
+-------------+---------------+---------------------------+--------------------------+-------------+
| <div>       | <div>         | <div>                     | <div>                    | <div>       |
|             |               |                           |                          |             |
| Info        | Informational | http://172.28.43.116:3000 | Percentage of endpoints  | 1 %         |
|             |               |                           | with content type        |             |
| </div>      | </div>        | </div>                    | text/css                 | </div>      |
|             |               |                           |                          |             |
|             |               |                           | </div>                   |             |
+-------------+---------------+---------------------------+--------------------------+-------------+
| <div>       | <div>         | <div>                     | <div>                    | <div>       |
|             |               |                           |                          |             |
| Info        | Informational | http://172.28.43.116:3000 | Percentage of endpoints  | 97 %        |
|             |               |                           | with content type        |             |
| </div>      | </div>        | </div>                    | text/html                | </div>      |
|             |               |                           |                          |             |
|             |               |                           | </div>                   |             |
+-------------+---------------+---------------------------+--------------------------+-------------+
| <div>       | <div>         | <div>                     | <div>                    | <div>       |
|             |               |                           |                          |             |
| Info        | Informational | http://172.28.43.116:3000 | Percentage of endpoints  | 4 %         |
|             |               |                           | with content type        |             |
| </div>      | </div>        | </div>                    | text/markdown            | </div>      |
|             |               |                           |                          |             |
|             |               |                           | </div>                   |             |
+-------------+---------------+---------------------------+--------------------------+-------------+
| <div>       | <div>         | <div>                     | <div>                    | <div>       |
|             |               |                           |                          |             |
| Info        | Informational | http://172.28.43.116:3000 | Percentage of endpoints  | 1 %         |
|             |               |                           | with content type        |             |
| </div>      | </div>        | </div>                    | text/plain               | </div>      |
|             |               |                           |                          |             |
|             |               |                           | </div>                   |             |
+-------------+---------------+---------------------------+--------------------------+-------------+
| <div>       | <div>         | <div>                     | <div>                    | <div>       |
|             |               |                           |                          |             |
| Info        | Informational | http://172.28.43.116:3000 | Percentage of endpoints  | 100 %       |
|             |               |                           | with method GET          |             |
| </div>      | </div>        | </div>                    |                          | </div>      |
|             |               |                           | </div>                   |             |
+-------------+---------------+---------------------------+--------------------------+-------------+
| <div>       | <div>         | <div>                     | <div>                    | <div>       |
|             |               |                           |                          |             |
| Info        | Informational | http://172.28.43.116:3000 | Count of total endpoints | 531         |
|             |               |                           |                          |             |
| </div>      | </div>        | </div>                    | </div>                   | </div>      |
+-------------+---------------+---------------------------+--------------------------+-------------+
| <div>       | <div>         | <div>                     | <div>                    | <div>       |
|             |               |                           |                          |             |
| Info        | Informational | http://172.28.43.116:3000 | Percentage of slow       | 4 %         |
|             |               |                           | responses                |             |
| </div>      | </div>        | </div>                    |                          | </div>      |
|             |               |                           | </div>                   |             |
+-------------+---------------+---------------------------+--------------------------+-------------+

: This table shows information that is likely to be very relevant to
you, but which is not related to vulnerabilities, or potentially even
related to the application in question.
:::
:::::::

::: {#alerts .section .alerts}
## Alerts

1.  ::: {#alerts--risk-2-confidence-3}
    ### Risk=[Medium]{.risk-level}, Confidence=[High]{.confidence-level} (2)

    1.  #### [http://172.28.43.116:3000]{.site} (2)

        1.  ##### [CSP: Failure to Define Directive with No Fallback](#alert-type-0) (1)

            1.  [GET
                http://172.28.43.116:3000/assets]{.request-method-n-url}
                +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------+
                | Alert tags                        | - [OWASP_2021_A05](https://owasp.org/Top10/2021/A05_2021-Security_Misconfiguration/)                                            |
                |                                   | - POLICY_QA_STD =                                                                                                               |
                |                                   | - POLICY_PENTEST =                                                                                                              |
                |                                   | - [SYSTEMIC](https://www.zaproxy.org/docs/desktop/addons/common-library/alerttags/#systemic)                                    |
                |                                   | - [CWE-693](https://cwe.mitre.org/data/definitions/693.html)                                                                    |
                |                                   | - [OWASP_2017_A06](https://owasp.org/www-project-top-ten/2017/A6_2017-Security_Misconfiguration.html)                           |
                |                                   | - [OWASP_2025_A02](https://owasp.org/Top10/2025/A02_2025-Security_Misconfiguration/)                                            |
                |                                   | - POLICY_DEV_STD =                                                                                                              |
                +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------+
                | Alert description                 | The Content Security Policy fails to define one of the directives that has no fallback. Missing/excluding them is the same as   |
                |                                   | allowing anything.                                                                                                              |
                +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------+
                | Other info                        | The directive(s): frame-ancestors, form-action is/are among the directives that do not fallback to default-src.                 |
                +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------+
                | Request                           | Request line and header section (243 bytes)                                                                                     |
                |                                   |     GET http://172.28.43.116:3000/assets HTTP/1.1                                                                               |
                |                                   |     host: 172.28.43.116:3000                                                                                                    |
                |                                   |     user-agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/141.0.0.0 Safari/537.36 |
                |                                   |     pragma: no-cache                                                                                                            |
                |                                   |     cache-control: no-cache                                                                                                     |
                |                                   |                                                                                                                                 |
                |                                   | Request body (0 bytes)                                                                                                          |
                +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------+
                | Response                          | Status line and header section (416 bytes)                                                                                      |
                |                                   |     HTTP/1.1 301 Moved Permanently                                                                                              |
                |                                   |     Access-Control-Allow-Origin: *                                                                                              |
                |                                   |     X-Content-Type-Options: nosniff                                                                                             |
                |                                   |     X-Frame-Options: SAMEORIGIN                                                                                                 |
                |                                   |     Feature-Policy: payment 'self'                                                                                              |
                |                                   |     X-Recruiting: /#/jobs                                                                                                       |
                |                                   |     Content-Type: text/html; charset=UTF-8                                                                                      |
                |                                   |     Content-Length: 156                                                                                                         |
                |                                   |     Content-Security-Policy: default-src 'none'                                                                                 |
                |                                   |     Location: /assets/                                                                                                          |
                |                                   |     Vary: Accept-Encoding                                                                                                       |
                |                                   |     Date: Sun, 20 Sep 2026 14:00:36 GMT                                                                                         |
                |                                   |     Connection: keep-alive                                                                                                      |
                |                                   |     Keep-Alive: timeout=5                                                                                                       |
                |                                   |                                                                                                                                 |
                |                                   | Response body (156 bytes)                                                                                                       |
                |                                   |     <!DOCTYPE html>                                                                                                             |
                |                                   |     <html lang="en">                                                                                                            |
                |                                   |     <head>                                                                                                                      |
                |                                   |     <meta charset="utf-8">                                                                                                      |
                |                                   |     <title>Redirecting</title>                                                                                                  |
                |                                   |     </head>                                                                                                                     |
                |                                   |     <body>                                                                                                                      |
                |                                   |     <pre>Redirecting to /assets/</pre>                                                                                          |
                |                                   |     </body>                                                                                                                     |
                |                                   |     </html>                                                                                                                     |
                +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------+
                | Parameter                         |     Content-Security-Policy                                                                                                     |
                +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------+
                | Evidence                          |     default-src 'none'                                                                                                          |
                +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------+
                | Solution                          | Ensure that your web server, application server, load balancer, etc. is properly configured to set the Content-Security-Policy  |
                |                                   | header.                                                                                                                         |
                +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------+
