---
layout: post
title: This is only a test
subtitle: A subtitle of the test by Kevin Weatherwalks
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/thumb.png
share-img: /assets/img/path.jpg
tags: [books, test]
---

How do we make a test post?

## Socrata Open Data
The dataset we will be using is from the data.delaware.gov open data portal \[[1]\] which we will access via Socrata's API endpoints. Through this endpoint, the data comes to us in JSON format and by default is limited to 1000 rows. We can get all the 653,417 rows of the data by appending the url with `$limit=654000` \[[2]\]

We set the significance at the standard $`\alpha=0.05`$ and correct for multiple comparisons using Bonferroni. With 3 comparisons we have an adjusted significance level, $`\alpha^\prime`$ for each test of about $`0.026`$. If our 

# References
[1]: https://data.delaware.gov/Education/Student-Discipline/yr4w-jdi4 "Student Discipline Data on the Open Data Portal"

[2]: https://support.socrata.com/hc/en-us/articles/202949268-How-to-query-more-than-1000-rows-of-a-dataset

[3]: https://www.aclu-de.org/en/analyzing-discipline-data-disrupting-disciplinary-cycle

[4]: https://www.ewa.org/blog-latino-ed-beat/research-examines-how-latino-students-are-disciplined

[5]: https://data.delaware.gov/Education/Student-Enrollment-by-Year-Organization-and-Race/utax-x8tw

[6]: https://nces.ed.gov/pubs2011/2011603.pdf

[7]: https://www2.ed.gov/admins/lead/account/stateplan17/decsa2017.pdf

[8]: https://studentprivacy.ed.gov/sites/default/files/resource_document/file/FAQs_disclosure_avoidance_0.pdf