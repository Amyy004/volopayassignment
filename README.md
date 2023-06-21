# volopayassignment
assignment
#include <iostream>
#include <cpprest/http_listener.h>
#include <cpprest/json.h>

using namespace web;
using namespace web::http;
using namespace web::http::experimental::listener;

// Structure to hold purchase information
struct Purchase
{
    int id;
    utility::datetime date;
    std::string user;
    std::string department;
    std::string software;
    int seats;
    double amount;
};

// Function to parse the dataset and retrieve purchases
std::vector<Purchase> parseDataset()
{
    // Implement the logic to parse the dataset
    // and retrieve the purchases

    // Here's an example implementation using the provided dataset
    std::vector<Purchase> purchases = {
        {1, utility::datetime::from_string("2022-10-23 18:50:10 +0530"), "Wil Wheaton", "Marketting", "Apple", 3, 60},
        {2, utility::datetime::from_string("2022-11-17 16:37:10 +0530"), "Leslie Winkle", "Tech", "Outplay", 1, 12},
        {3, utility::datetime::from_string("2022-05-14 09:00:57 +0530"), "Amy Farrah Fowler", "Tech", "Outplay", 3, 36},
        {4, utility::datetime::from_string("2023-02-03 14:37:45 +0530"), "Bert Kibbler", "Customer Success", "Sentry", 1, 5}
    };

    return purchases;
}

// API endpoint handler for "/api/percentage_of_department_wise_sold_items"
void handlePercentageOfDepartmentWiseSoldItems(http_request request)
{
    // Retrieve the query parameters
    auto parameters = uri::split_query(request.request_uri().query());
    utility::datetime startDate, endDate;
    try
    {
        startDate = utility::datetime::from_string(parameters.at("start_date"));
        endDate = utility::datetime::from_string(parameters.at("end_date"));
    }
    catch (const std::out_of_range& e)
    {
        request.reply(status_codes::BadRequest, "Invalid or missing query parameters.");
        return;
    }

    // Parse the dataset and retrieve purchases
    std::vector<Purchase> purchases = parseDataset();

    // Calculate the total sold items for each department
    std::unordered_map<std::string, int> departmentSoldItems;
    for (const auto& purchase : purchases)
    {
        if (purchase.date >= startDate && purchase.date <= endDate)
        {
            departmentSoldItems[purchase.department] += purchase.seats;
        }
    }

    // Calculate the percentage of sold items department-wise
    json::value response;
    for (const auto& item : departmentSoldItems)
    {
        double percentage = static_cast<double>(item.second) / totalSoldItems * 100;
        response[utility::conversions::to_string_t(item.first)] = json::value::number(percentage);
    }

    // Send the response
    request.reply(status_codes::OK, response);
}

int main()
{
    // Create an HTTP listener
    http_listener listener("http://localhost:5000/api/");

    // Add route handlers
    listener.support(methods::GET, "total_items", handleTotalItems);
    listener.support(methods::GET, "nth_most_total_item", handleNthMostTotalItem);
    listener.support(methods::GET, "percentage_of_department_wise_sold_items", handlePercentageOfDepartmentW

