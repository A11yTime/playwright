test('Check accessibility of the page', async ({ page }) => {
  await page.goto('https://www.fidelity.com/');
  await page.addScriptTag({ path: require.resolve('axe-core') });

  const result = await page.evaluate(() => {
    return new Promise((resolve) => {
      window.axe.run(document, {}, (err, results) => {
        if (err) {
          console.error('Axe failed to run:', err);
          resolve([]);
        }
        resolve(results.violations);
      });
    });
  });

  // Check for specific violations (for example, "page-has-heading-one")
  const headingViolation = result.find(violation => violation.id === 'page-has-heading-one');
  expect(headingViolation).toBeDefined(); // Ensure that a heading is found

  // You can modify this to check specific violations and their severity as well
  expect(result.length).toBe(0); // Or adjust based on known expected issues
});
