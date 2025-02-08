test('Check accessibility of the page', async ({ page }) => {
  // Navigate to the webpage you want to test
  await page.goto('https://example.com');

  // Inject axe-core into the page
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
          resolve(results.violations);  // Return violations
        });
      } else {
        resolve([]);
      }
    });
  });

  // Log the results to inspect violations
  console.log('Accessibility Violations:', result);

  // Filter critical violations (you can adjust this based on your needs)
  const criticalViolations = result.filter(v => v.impact === 'critical');
  
  // Assert that there are no critical violations
  expect(criticalViolations.length).toBe(0);
});
