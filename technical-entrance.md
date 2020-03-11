# Welcome to Hoozing's technical entrance

### There are 3 questions, please take a deep breath, keep calm and answer the questions.

### Submission Guideline:
- Fork (copy) this repository.
- Clone the forked repository in your local computer.
- Open the repository folder, create file(s) to store your answers in the submission folder.
- Answering the 3 questions.
- Commit your changes.
- Push the commit(s) to your public repository in github.
- Send us the url of your github repository so that we can view your answers.

##  1. Write an algorithm to find missing number

####  Given an array of size n-1 and given that there are numbers from 1 to n with one missing, write an function to find the missing number

- For example:
 *Input:* `[2,3,1,4,6,9,7,8,10,7]`
 *Output:* `5`

example sai rồi bạn ơi !!!!
lenght là n-1 = 9 mới đúng
bỏ số 7 đi mà code:

int sum = n*(n+1)/2;
for each item in arr
sum -= item;

return sum;



## 2. Refactoring the following method
####  Refactor the source code and correct the flow. Feel free to add/remove any blocks

    public static bool IsValidEmail(string email)
    {
        try
        {
            email = Regex.Replace(email, @"(@)(.+)$", DomainMapper,
                                  RegexOptions.None, TimeSpan.FromMilliseconds(200));

            string DomainMapper(Match match)
            {
                var idn = new IdnMapping();

                var domainName = idn.GetAscii(match.Groups[2].Value);

                return match.Groups[1].Value + domainName;
            }
        }
        catch (RegexMatchTimeoutException e)
        {
            return false;
        }
        catch (ArgumentException e)
        {
            return false;
        }

        try
        {
            return Regex.IsMatch(email,
                @"^(?("")("".+?(?<!\\)""@)|(([0-9a-z]((\.(?!\.))|[-!#\$%&'\*\+/=\?\^`\{\}\|~\w])*)(?<=[0-9a-z])@))" +
                @"(?(\[)(\[(\d{1,3}\.){3}\d{1,3}\])|(([0-9a-z][-0-9a-z]*[0-9a-z]*\.)+[a-z0-9][\-a-z0-9]{0,22}[a-z0-9]))$",
                RegexOptions.IgnoreCase, TimeSpan.FromMilliseconds(250));
        }
        catch (RegexMatchTimeoutException)
        {
            return false;
        }
    }
    
    public void Register(UserModel user) {

        bool validate = true;
	    string errorMessage = "";

	    if (String.IsNullOrEmpty(user.UserName)) {
		    errorMessage += "You must specify a username.";
		    validate = false;
	    }else if (String.IsNullOrEmpty(user.Password)) {
		    errorMessage += "You must specify a Password.";
		    validate = false;
	    }else if (user.Password.Length < 6) {
		    errorMessage += String.Format("You must specify a password of {0} or more characters.",  6);
		    validate = false;
	    }else if (!String.Equals(user.Password, user.ConfirmPassword, StringComparison.Ordinal)) {
		    errorMessage += "The new password and confirmation password do not match.";
		    validate = false;
	    }else if (String.IsNullOrEmpty(user.Email)) {
		    errorMessage += "You must specify an email address.";
		    validate = false;
	    }else if(!IsValidEmail(user.Email)) {
		    errorMessage += "You must specify a valid email address.";
		    validate = false;
	    }

        if(validate){
            bool isExisted =  false;
            var existedUser = _userService.GetUserByUsername(user.Username);
            if  (existedUser != null) {
                errorMessage += "User with that username and/or email already exists.";
                validate = false;
            }
        }
    
	    if  (validate) {
            try {
                Regex RgxUrl = new  Regex("[^a-z0-9]");
                var isContainsSpecialCharacters = RgxUrl.IsMatch(user.Username);
                if  (isContainsSpecialCharacters) {
                    _userService.SaveUser(user);
                }
            
                MailMessage message =  new  MailMessage();
                message.To.Add(user.Email);
                message.Subject = "Register";
                message.From = new  System.Net.Mail.MailAddress("admin@hotmail.com");
                message.Body = "Thank you for your register";
                SmtpClient smtp = new SmtpClient();
                smtp.Host = "smtp.gmail.com";
                smtp.Send(message);
                
            }  catch(Exception) {
            }
	    }
    }


## 3. Write script in SQL Server

####  There are 3 tables as below, write an SQL query to retrieve the best selling product of last month. 
####  The accepted query should have 2 fields: `Product Name`, `NumberOfSellingProducts`


> Order Table

| Field Name | Data Type |
|--|--|
| Id | int |
| Date | DateTime |
| Total| double|

> Order Detail Table

| Field Name | Data Type |
|--|--|
| Id | int |
| Order Id | int|
| Quality| int |
| Price| double|
| ProductId| int |
| Amount| double|

> Product Table

| Field Name | Data Type |
|--|--|
| Id | int |
| Name | string|


SELECT TOP 1 C.Name AS [Product Name], C.Total AS [NumberOfSellingProducts]
FROM (
    SELECT Product.Name, Order.Total
    FROM Order;
    LEFT JOIN OrderDetail
    ON OrderTable.Id = OrderDetailTable.Id
    LEFT JOIN Product
    ON OrderDetailTable.ProductId = Product.Id
    WHERE DATEPART(m, Order.Date) = DATEPART(m, DATEADD(m, -1, getdate()))
    AND DATEPART(yyyy, Order.Date) = DATEPART(yyyy, DATEADD(m, -1, getdate()))
) AS C
ORDER BY C.Total;


