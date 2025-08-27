# Automated-Tests-
1. Login with valid credentials
import org.openqa.selenium.By;
import org.testng.Assert;
import org.testng.annotations.Test;

public class LoginTests extends BaseTest {

    @Test
    public void validLogin() {
        driver.findElement(By.id("user-name")).sendKeys("standard_user");
        driver.findElement(By.id("password")).sendKeys("secret_sauce");
        driver.findElement(By.id("login-button")).click();

        String url = driver.getCurrentUrl();
        Assert.assertTrue(url.contains("inventory"), "User not redirected to inventory page!");
    }
}
2. Login with invalid credentials
@Test
public void invalidLogin() {
    driver.findElement(By.id("user-name")).sendKeys("wrong_user");
    driver.findElement(By.id("password")).sendKeys("wrong_pass");
    driver.findElement(By.id("login-button")).click();

    String error = driver.findElement(By.cssSelector("h3[data-test='error']")).getText();
    Assert.assertTrue(error.contains("Username and password do not match"));
}


3. Add item to cart and verify count
@Test
public void addToCart() {
    driver.findElement(By.id("user-name")).sendKeys("standard_user");
    driver.findElement(By.id("password")).sendKeys("secret_sauce");
    driver.findElement(By.id("login-button")).click();

    driver.findElement(By.id("add-to-cart-sauce-labs-backpack")).click();
    String cartCount = driver.findElement(By.className("shopping_cart_badge")).getText();

    Assert.assertEquals(cartCount, "1");
}

4. Add multiple items and verify total price
@Test
public void addMultipleItems() {
    driver.findElement(By.id("user-name")).sendKeys("standard_user");
    driver.findElement(By.id("password")).sendKeys("secret_sauce");
    driver.findElement(By.id("login-button")).click();

    driver.findElement(By.id("add-to-cart-sauce-labs-backpack")).click();
    driver.findElement(By.id("add-to-cart-sauce-labs-bike-light")).click();
    driver.findElement(By.className("shopping_cart_link")).click();

    String item1 = driver.findElement(By.cssSelector(".cart_item:nth-child(3) .inventory_item_price")).getText();
    String item2 = driver.findElement(By.cssSelector(".cart_item:nth-child(4) .inventory_item_price")).getText();

    double total = Double.parseDouble(item1.replace("$", "")) + Double.parseDouble(item2.replace("$", ""));
    driver.findElement(By.id("checkout")).click();
    driver.findElement(By.id("first-name")).sendKeys("Test");
    driver.findElement(By.id("last-name")).sendKeys("User");
    driver.findElement(By.id("postal-code")).sendKeys("12345");
    driver.findElement(By.id("continue")).click();

    String summaryTotal = driver.findElement(By.cssSelector(".summary_subtotal_label")).getText();
    Assert.assertTrue(summaryTotal.contains(String.valueOf(total)));
}

5. Remove item from cart
@Test
public void removeFromCart() {
    driver.findElement(By.id("user-name")).sendKeys("standard_user");
    driver.findElement(By.id("password")).sendKeys("secret_sauce");
    driver.findElement(By.id("login-button")).click();

    driver.findElement(By.id("add-to-cart-sauce-labs-backpack")).click();
    driver.findElement(By.id("remove-sauce-labs-backpack")).click();

    boolean isCartEmpty = driver.findElements(By.className("shopping_cart_badge")).isEmpty();
    Assert.assertTrue(isCartEmpty);
}

6. Checkout flow with valid info
@Test
public void checkoutValidInfo() {
    driver.findElement(By.id("user-name")).sendKeys("standard_user");
    driver.findElement(By.id("password")).sendKeys("secret_sauce");
    driver.findElement(By.id("login-button")).click();

    driver.findElement(By.id("add-to-cart-sauce-labs-backpack")).click();
    driver.findElement(By.className("shopping_cart_link")).click();
    driver.findElement(By.id("checkout")).click();

    driver.findElement(By.id("first-name")).sendKeys("Test");
    driver.findElement(By.id("last-name")).sendKeys("User");
    driver.findElement(By.id("postal-code")).sendKeys("12345");
    driver.findElement(By.id("continue")).click();
    driver.findElement(By.id("finish")).click();

    String confirmation = driver.findElement(By.className("complete-header")).getText();
    Assert.assertTrue(confirmation.contains("THANK YOU"));
}

7. Checkout with blank required field
@Test
public void checkoutMissingInfo() {
    driver.findElement(By.id("user-name")).sendKeys("standard_user");
    driver.findElement(By.id("password")).sendKeys("secret_sauce");
    driver.findElement(By.id("login-button")).click();

    driver.findElement(By.className("shopping_cart_link")).click();
    driver.findElement(By.id("checkout")).click();

    driver.findElement(By.id("continue")).click();
    String error = driver.findElement(By.cssSelector("h3[data-test='error']")).getText();
    Assert.assertTrue(error.contains("First Name is required"));
}

8. Verify sorting by Price (Low–High)
@Test
public void sortLowToHigh() {
    driver.findElement(By.id("user-name")).sendKeys("standard_user");
    driver.findElement(By.id("password")).sendKeys("secret_sauce");
    driver.findElement(By.id("login-button")).click();

    driver.findElement(By.className("product_sort_container")).sendKeys("Price (low to high)");
    String firstPrice = driver.findElement(By.cssSelector(".inventory_item_price")).getText();

    Assert.assertEquals(firstPrice, "$7.99");
}

9. Verify sorting by Name (A–Z)
@Test
public void sortAToZ() {
    driver.findElement(By.id("user-name")).sendKeys("standard_user");
    driver.findElement(By.id("password")).sendKeys("secret_sauce");
    driver.findElement(By.id("login-button")).click();

    driver.findElement(By.className("product_sort_container")).sendKeys("Name (A to Z)");
    String firstProduct = driver.findElement(By.className("inventory_item_name")).getText();
    Assert.assertEquals(firstProduct, "Sauce Labs Backpack");
}

10. Logout and ensure user is redirected
@Test
public void logoutTest() {
    driver.findElement(By.id("user-name")).sendKeys("standard_user");
    driver.findElement(By.id("password")).sendKeys("secret_sauce");
    driver.findElement(By.id("login-button")).click();

    driver.findElement(By.id("react-burger-menu-btn")).click();
    driver.findElement(By.id("logout_sidebar_link")).click();

    Assert.assertTrue(driver.getCurrentUrl().contains("saucedemo.com"));
}
