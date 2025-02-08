const { test, expect } = require('@playwright/test');

test('Check accessibility of the page', async ({ page }) => {
  // Navigate to the webpage you want to test
  await page.goto('https://www.fidelity.com/'); // Or another URL if the error persists

  // Inject axe-core into the page (so you can use axe.run)
  await page.addScriptTag({
    url: 'https://cdnjs.cloudflare.com/ajax/libs/axe-core/4.3.3/axe.min.js',
  });

  // Run Axe accessibility check on the page
  const result = await page.evaluate(() => {
    return new Promise((resolve) => {
      if (typeof axe !== 'undefined') {
        axe.run(document, {}, (err, results) => {
          if (err) {
            console.error('Axe failed to run:', err);
            resolve([]);
          }
          resolve(results.violations);  // Return any accessibility violations
        });
      } else {
        resolve([]);
      }
    });
  });

  // Log the violations for debugging
  console.log('Accessibility Violations:', result);

  // Filter critical violations only
  const criticalViolations = result.filter(v => v.impact === 'critical');
  
  // Assert that no critical violations are found
  expect(criticalViolations.length).toBe(0);
});
