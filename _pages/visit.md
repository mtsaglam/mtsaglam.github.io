---
title: "Site Statistics"
permalink: /visit/
layout: default
---

# Visitor Analytics

This page shows real-time statistics about visitors to this website.

<div id="analytics-container" style="margin-top: 2rem;">
  <div style="text-align: center; padding: 2rem; background: #f5f5f5; border-radius: 8px; margin-bottom: 2rem;">
    <p style="font-size: 0.9em; color: #666;">Loading analytics data...</p>
  </div>
</div>

{% raw %}
<script>
  const SUPABASE_URL = 'https://wfpybepruwgmkxnkpdqp.supabase.co';
  const SUPABASE_KEY = 'sb_publishable_YhTRqOYQkAGcKUGF_mp4bA_B6ZyXf6L';
  
  async function loadAnalytics() {
    const container = document.getElementById('analytics-container');
    
    try {
      // Fetch all visitors
      const response = await fetch(`${SUPABASE_URL}/rest/v1/visitors?select=*`, {
        headers: {
          'Authorization': `Bearer ${SUPABASE_KEY}`,
          'Accept': 'application/json'
        }
      });
      
      if (!response.ok) {
        throw new Error(`API error: ${response.status}`);
      }
      
      const visitors = await response.json();
      
      // Process data
      let totalVisits = visitors.length;
      let uniqueIPs = new Set(visitors.map(v => v.ip_address)).size;
      let visitorsByLocation = {};
      let dailyStats = {};
      
      visitors.forEach(visit => {
        const location = `${visit.country}${visit.city ? ', ' + visit.city : ''}`;
        visitorsByLocation[location] = (visitorsByLocation[location] || 0) + 1;
        
        const date = new Date(visit.timestamp).toISOString().split('T')[0];
        dailyStats[date] = (dailyStats[date] || 0) + 1;
      });
      
      // Sort and slice data
      const sortedLocations = Object.entries(visitorsByLocation)
        .sort((a, b) => b[1] - a[1])
        .slice(0, 10);
      
      const dailyEntries = Object.entries(dailyStats)
        .sort((a, b) => b[0].localeCompare(a[0]))
        .slice(0, 7);
      
      // Build HTML
      let html = `
        <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 1rem; margin-bottom: 2rem;">
          <div style="padding: 1.5rem; background: #e3f2fd; border-radius: 8px; border-left: 4px solid #1976d2;">
            <h3 style="margin-top: 0; color: #1976d2;">Total Unique IPs</h3>
            <p style="font-size: 2.5em; font-weight: bold; margin: 0.5rem 0; color: #1565c0;">${uniqueIPs}</p>
          </div>
          <div style="padding: 1.5rem; background: #f3e5f5; border-radius: 8px; border-left: 4px solid #7b1fa2;">
            <h3 style="margin-top: 0; color: #7b1fa2;">Total Page Views</h3>
            <p style="font-size: 2.5em; font-weight: bold; margin: 0.5rem 0; color: #6a1b9a;">${totalVisits}</p>
          </div>
        </div>

        <h3 style="margin-top: 2rem; border-bottom: 2px solid #e0e0e0; padding-bottom: 0.5rem;">Recent Activity (Last 7 Days)</h3>
        <table style="width: 100%; border-collapse: collapse; margin-bottom: 2rem;">
          <thead>
            <tr style="background: #f5f5f5;">
              <th style="padding: 0.75rem; text-align: left; border-bottom: 2px solid #ddd;">Date</th>
              <th style="padding: 0.75rem; text-align: right; border-bottom: 2px solid #ddd;">Views</th>
            </tr>
          </thead>
          <tbody>
            ${dailyEntries.map(([date, count]) => `
              <tr style="border-bottom: 1px solid #e5e5e5;">
                <td style="padding: 0.75rem;">${new Date(date).toLocaleDateString('en-US', {weekday: 'short', month: 'short', day: 'numeric'})}</td>
                <td style="padding: 0.75rem; text-align: right; font-weight: 500;">${count}</td>
              </tr>
            `).join('')}
          </tbody>
        </table>

        <h3 style="border-bottom: 2px solid #e0e0e0; padding-bottom: 0.5rem;">Visitors by Location (Top 10)</h3>
        <table style="width: 100%; border-collapse: collapse;">
          <thead>
            <tr style="background: #f5f5f5;">
              <th style="padding: 0.75rem; text-align: left; border-bottom: 2px solid #ddd;">Location</th>
              <th style="padding: 0.75rem; text-align: right; border-bottom: 2px solid #ddd;">Visits</th>
            </tr>
          </thead>
          <tbody>
            ${sortedLocations.map(([location, count]) => `
              <tr style="border-bottom: 1px solid #e5e5e5;">
                <td style="padding: 0.75rem;">${location}</td>
                <td style="padding: 0.75rem; text-align: right; font-weight: 500;">${count}</td>
              </tr>
            `).join('')}
          </tbody>
        </table>

        <p style="margin-top: 2rem; padding: 1rem; background: #fff3e0; border-radius: 4px; font-size: 0.9em; color: #666;">
          📊 <strong>Note:</strong> Analytics data is updated in real-time. This page shows visitor information based on IP geolocation data.
        </p>
      `;
      
      container.innerHTML = html;
    } catch (error) {
      container.innerHTML = `
        <div style="padding: 2rem; background: #ffebee; border-radius: 8px; border-left: 4px solid #c62828;">
          <p style="color: #c62828; margin: 0;">
            <strong>Error loading analytics:</strong> ${error.message}
          </p>
        </div>
      `;
    }
  }
  
  // Load analytics when page loads
  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', loadAnalytics);
  } else {
    loadAnalytics();
  }
</script>
{% endraw %}
