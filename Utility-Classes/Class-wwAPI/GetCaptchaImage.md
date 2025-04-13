Creates a CAPTCHA image with the specified text. CAPTCHA images are meant to use for validation of users on a Web site to protect from spamming.

The image is returned as a file and you can use any GDI+ compatible file type by specifying the appropriate extension (.gif, .jpg, .png etc.). The image is automatically sized based on your font and fontsize. You might have to experiment with the sizing.

Note: This is a static function not a method