PWA

听闻PWA也已经有一段时间了，但一直不知具体为何物，惭愧，16年的概念……今天大致了解了一下。

总的来说，作为一个小白根据我的理解Progressive Web App即是表现向原生App靠近的Web App。基于现代浏览器技术，PWA能够尽可能地在各种环境不限于浏览器与操作系统中都可以流畅使用，同时能够离线工作，并且可以直接被用户安装到桌面，最吸引我注意的还有PWA拥有broadcast功能。以及其他更多令开发者们关注的特性如使用Service workers持续更新、全面采用HTTPS、能够进行SEO优化等等。也就是一个Web App同时拥有了类原生App的许多便利特性，带来更好的用户体验。

大体而言PWA的基础是manifest.json和Service Workers，顺带也讲一讲Service workers吧。Service workers是在浏览器后台运行的独立于网页的脚本，因而可以支持离线体验，在此之前充当同样角色的是HTML5的application cache，即AppCache（好的，继续惭愧，我没有实际用过，仅仅许久之前在高程上看过相关的章节）。因为service workers在后台运行，无法直接操作DOM，但可以通过postMessage接口与页面通信进而令页面对DOM执行相应操作。最为重要的是Service workers可以控制并处理页面发送的request，更多介绍可查看[官方文档](https://developers.google.com/web/fundamentals/primers/service-workers/)。

PWA的一个主要目标是令Web App尽可能在各种使用场景中都可以正常运作，包括无网络连接时。PWA将Web App的核心基础结构、UI界面和内容data分离，通过Service workers对基本的app shell和UI部分进行缓存，每次开启时需要向服务器请求的就只剩下具体的data了，听起来就像原生App的使用流程对吧？因此开发PWA时App shell的架构意识是很重要的，哪些部分是最基础最核心的，每次开启时哪些部分时要立即首屏显示的，基本的app shell所需的样式与js资源等等。同时也要认真思考PWA首次运行时预先加载的数据，以及后续运行时相应数据是否被替换与更改。

根据[caniuse](https://caniuse.com/#search=service%20workers)的统计，截止至此篇笔记编写的17年12月16日，目前PWA的浏览器支持程度大体为（仅针对使用最广泛的普通用户版本）：

- IE 不支持
- Edge 从17开始支持
- Firefox 从44开始支持，33 - 43版本及部分ESR版本可以通过dom.serviceWorkers.enabled进行使用
- Chrome 从45开始全面支持
- Safari 不支持
- 移动端支持率堪忧，大体也就Chrome和Firefox支持，safari与IE仍旧不支持，其他浏览器部分支持，不过微信内嵌的X5内核已经支持了

然而PWA个人感觉在国内并不怎么普及，而且针对国内五花八门的浏览器和系统，Service workers API的支持情况估计也十分尴尬……而且由于Service workers直接控制网络请求，感觉容易出现很多炸裂的情况，代码里的bug可能会被缓存，service workers一旦出错很大几率导致所有页面无法正常工作等等，对于我这种小白大概也只能照着官方例子学学，之后来劲儿了自己私下玩玩而已了。

参照[官方例子](https://developers.google.com/web/fundamentals/codelabs/your-first-pwapp/)小白要开始尝试玩PWA也是很简易的，有基础的HTML和相应的CSS样式文件、JS脚本足矣。开发流程其实就是普通的前端项目流程，重点在于PWA特性的具体实现上。对于Goole developers给出的这个简单例子，loading也算入App shell内。这是一个想当然但是实际开发中可能会忽视或弃用的细节，实际上有提示性的首屏加载比起内容空白的首屏其用户体验是要好非常多的。