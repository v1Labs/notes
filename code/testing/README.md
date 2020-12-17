# Testing Philosophy

When it comes to writing tests code, my opinion seems to be in the minority. I believe you should automatically test the minimum number of things necessary to guarantee a change will not impede an application's ability to achieve its primary purpose.

This differs from the popular opinion. A lot of people believe EVERYTHING should be tested. The implication is that any change should not negatively impact the application in any way.

I find the "test everything" approach to be far too conservative. Building an application that puts a premium on stability suffers from the self-imposed immutability.

Instead, we should be building systems that invite change by creating a higher tolerance for undesired side effects. For this, we should only automatically test what is critical, and manually test everything else. It should be expected that manual testing will result in bugs and less than perfect user experiences. Optimizing to minimize the cost of change is worth the trade-off.
