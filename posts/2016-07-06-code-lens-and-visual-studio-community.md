---
id: 675
title: Code Lens and Visual Studio Community
date: 2016-07-06T12:07:29+00:00
author: Etienne
layout: post
guid: http://geeksperiment.com/?p=675
permalink: /code-lens-and-visual-studio-community/
categories:
  - Uncategorized
format: aside
---
# Code Lens and Visual Studio Community

Code Lens is a powerful plugin of Visual Studio (available since 2013 version).

It shows various information, but the one I found the most useful is the references count. I&rsquo;m talking about this:
  
[<img src="http://geeksperiment.com/wp-content/uploads/2016/07/CodeLens.png" alt="CodeLens" width="585" height="176" class="aligncenter size-full wp-image-676" srcset="http://geeksperiment.com/wp-content/uploads/2016/07/CodeLens.png 585w, http://geeksperiment.com/wp-content/uploads/2016/07/CodeLens-300x90.png 300w" sizes="(max-width: 585px) 100vw, 585px" />](http://geeksperiment.com/wp-content/uploads/2016/07/CodeLens.png)

### But&#8230;

&#8230;it&rsquo;s not available in Visual Studio Community Edition. Code Lens is part of the &laquo;&nbsp;premium&nbsp;&raquo; features you get when you have Pro or Enterprise edition. (VS Team confirms this : <a href="https://visualstudio.uservoice.com/forums/121579-visual-studio-2015/suggestions/8943250-codelens-for-community-edition" target="_blank">Visual Studio Uservoice</a>)

### But&#8230;(again)

&#8230;there is a workaround! Just install the SSDT (SQL Server Data Tools) preview for Visual Studio 2015. You can download it here : <a href="https://msdn.microsoft.com/en-us/mt429383.aspx?f=255&#038;MSPPError=-2147217396" target="_blank">https://msdn.microsoft.com/en-us/mt429383.aspx?f=255&MSPPError=-2147217396</a>

This installs the Code Lens plugin and allows you to enable it in VS :
  
[<img src="http://geeksperiment.com/wp-content/uploads/2016/07/VS_Settings_CodeLens.png" alt="VS_Settings_CodeLens" width="544" height="300" class="aligncenter size-full wp-image-679" srcset="http://geeksperiment.com/wp-content/uploads/2016/07/VS_Settings_CodeLens.png 544w, http://geeksperiment.com/wp-content/uploads/2016/07/VS_Settings_CodeLens-300x165.png 300w" sizes="(max-width: 544px) 100vw, 544px" />](http://geeksperiment.com/wp-content/uploads/2016/07/VS_Settings_CodeLens.png)

<u>Source :</u> <a href="http://stackoverflow.com/questions/33144785/missing-codelens-references-count-in-vs-2015-community-edition" target="_blank">StackOverflow</a>