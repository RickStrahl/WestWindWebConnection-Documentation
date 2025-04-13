Converts an ISO or MS AJAX style date into a date object.

If the date string is empty or doesn't follow the ISO or MS AJAX string format null is returned.

Use this method on date strings returned with the .parse() method. Manually calling this method for each date you actually use can be more efficient than using .parseWithDate() which has to parse through all properties to find dates and thus can add significant overhead in large object/array graphs.