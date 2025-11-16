# SEO Improvement Action Plan

## Current SEO Issues Identified

### Critical Issues (Fix Immediately)

1. **HTTP instead of HTTPS** - Major ranking penalty
2. **Missing meta descriptions** - Hurts click-through rates
3. **No Google Search Console verification** - Not indexed properly
4. **Limited content** - Only 1 blog post

### Medium Priority Issues

5. **Missing Twitter username** - Incomplete social media integration
6. **No structured data** - Missing rich snippets opportunity
7. **No internal linking** - Poor content connectivity

## Step-by-Step Fix Guide

### 1. Fix HTTPS Configuration (CRITICAL)

**File:** `_config.yml`

Change:
```yaml
url: "http://aribiswas.github.io"
```

To:
```yaml
url: "https://aribiswas.github.io"
```

**Impact:** This will fix all sitemap URLs and meta tags to use HTTPS.

**Action:** After changing, rebuild and redeploy:
```bash
bundle exec jekyll build
git add _config.yml
git commit -m "fix: update URL to use HTTPS for SEO"
git push
```

---

### 2. Add Meta Description to Homepage

**File:** `_config.yml`

Update the description to be more detailed:
```yaml
description: >-
  I write about topics on artificial intelligence and software engineering.
```

Change to something more compelling:
```yaml
description: >-
  Technical blog by Ari Biswas covering artificial intelligence, robotics,
  reinforcement learning, and software engineering. Learn about cutting-edge
  topics like quadruped robot training, MuJoCo simulations, and MATLAB integration.
```

---

### 3. Add Twitter Username (Optional but Recommended)

**File:** `_config.yml`

If you have a Twitter/X account, uncomment and add:
```yaml
twitter_username: your_twitter_handle
```

This enables proper Twitter card previews.

---

### 4. Submit to Google Search Console

**Steps:**

1. Go to [Google Search Console](https://search.google.com/search-console/)
2. Add property: `https://aribiswas.github.io`
3. Verify ownership using one of these methods:
   - **HTML file upload** (easiest for GitHub Pages)
   - **HTML meta tag** (add to theme)
   - **Google Analytics** (you already have gtag configured)
   - **DNS record**

4. After verification, submit sitemap:
   - Go to "Sitemaps" section
   - Submit: `https://aribiswas.github.io/sitemap.xml`

5. Request indexing for key pages:
   - Submit homepage
   - Submit your blog post URL

**Expected Result:** Google will crawl and index your site within 1-7 days.

---

### 5. Verify Google Analytics Integration

You have Google Analytics configured:
```yaml
gtag: "G-KDT2BM43QG"
```

**Action Steps:**
1. Log into [Google Analytics](https://analytics.google.com/)
2. Verify the site is receiving traffic data
3. Link Search Console to Analytics for better insights

---

### 6. Create More Content (Long-term Strategy)

**Why:** Google favors active sites with quality content.

**Recommendations:**
- Aim for at least 1 blog post per month
- Each post should be 800+ words
- Focus on your expertise: AI, robotics, RL, MATLAB
- Use consistent formatting and SEO metadata

**Content Ideas:**
- Follow-up to Unitree robot training (improvements, challenges)
- MATLAB tips for robotics engineers
- Reinforcement learning algorithm comparisons
- MuJoCo simulation best practices
- Software engineering practices for AI projects

---

### 7. Add Structured Data (Schema.org Markup)

**What:** JSON-LD structured data helps Google understand your content.

**How:** Create `_includes/json-ld.html`:

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "BlogPosting",
  "headline": "{{ page.title }}",
  "description": "{{ page.excerpt | strip_html | strip_newlines | truncate: 160 }}",
  "image": "{{ site.url }}{{ page.thumbnail-img }}",
  "author": {
    "@type": "Person",
    "name": "{{ site.author }}"
  },
  "publisher": {
    "@type": "Person",
    "name": "{{ site.author }}"
  },
  "datePublished": "{{ page.date | date_to_xmlschema }}",
  "dateModified": "{{ page.date | date_to_xmlschema }}"
}
</script>
```

Note: Beautiful Jekyll may already include this - check theme files.

---

### 8. Optimize Blog Post SEO

Your current post has good SEO metadata, but verify each future post includes:

```yaml
---
layout: post
title: "Clear, descriptive title"
subtitle: "Engaging subtitle"
excerpt: "150-160 character description for previews"
seo_title: "Keyword-optimized title (if different from title)"
seo_description: "150-160 character meta description with keywords"
tags: [keyword1, keyword2, keyword3]  # Use relevant search terms
thumbnail-img: assets/YYYY-MM-DD-title/image.png
date: YYYY-MM-DD HH:MM:SS -0500
---
```

**Post Content SEO Tips:**
- Use H2 (##) and H3 (###) headers with keywords
- Include alt text for images: `![alt text](/path/to/image.png)`
- Add internal links between related posts
- Target long-tail keywords (e.g., "train quadruped robot with MuJoCo")
- Minimum 800 words per post

---

### 9. Add robots.txt Enhancements

Current robots.txt is minimal. Consider creating a custom one:

**File:** Create `robots.txt` in root directory:

```
User-agent: *
Allow: /
Sitemap: https://aribiswas.github.io/sitemap.xml

# Block common bot traps
Disallow: /assets/fonts/
```

---

### 10. Build Quality Backlinks

**Strategies:**
1. Share your blog posts on:
   - LinkedIn (you have profile: biswasic)
   - Reddit (r/MachineLearning, r/robotics)
   - Hacker News
   - Twitter/X

2. Engage with robotics/AI communities
3. Comment on related blogs with your site link
4. Submit to:
   - Medium (cross-post or link back)
   - Dev.to (with canonical URL)
   - Relevant AI/robotics newsletters

---

## Priority Order

### Week 1 - Critical Fixes
1. ✅ Change HTTP to HTTPS in config
2. ✅ Update site description
3. ✅ Rebuild and deploy
4. ✅ Submit to Google Search Console
5. ✅ Submit sitemap
6. ✅ Request indexing for existing pages

### Week 2 - Verification
7. ✅ Verify Google Search Console shows pages indexed
8. ✅ Check Google Analytics data flow
9. ✅ Monitor search appearance (may take 1-4 weeks)

### Ongoing - Content & Growth
10. ✅ Publish 1-2 new blog posts monthly
11. ✅ Share content on social media
12. ✅ Build backlinks gradually
13. ✅ Monitor Search Console performance

---

## Expected Timeline

- **Immediate (after HTTPS fix):** Sitemap regenerates with HTTPS URLs
- **1-3 days:** Google Search Console verification complete
- **3-7 days:** Initial crawling and indexing begins
- **2-4 weeks:** First appearances in Google search results
- **2-3 months:** Established rankings for target keywords

---

## Monitoring Tools

1. **Google Search Console**: Track indexing, rankings, clicks
2. **Google Analytics**: Monitor traffic sources and user behavior
3. **Manual Search Tests**: Search for "site:aribiswas.github.io"
4. **Keyword Tracking**: Track rankings for key phrases like:
   - "train unitree robot"
   - "mujoco matlab integration"
   - "quadruped robot reinforcement learning"

---

## Additional Resources

- [Google SEO Starter Guide](https://developers.google.com/search/docs/fundamentals/seo-starter-guide)
- [Beautiful Jekyll SEO Features](https://beautifuljekyll.com/faq/#seo)
- [Jekyll SEO Tag Plugin](https://github.com/jekyll/jekyll-seo-tag)
- [Schema.org Documentation](https://schema.org/BlogPosting)
