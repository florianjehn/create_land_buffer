def create_seaweed_land_buffer(
    file_countries, file_harbors, buffer_country, buffer_harbor
):
    """
    Creates a buffer around harbors and countries and saves it GeoJSON.
    Arguments:
        file_countries: path to the file with the countries
        file_harbors: path to the file with the harbors
        buffer_harbor: size buffer around harbors (km)
        buffer_country: size buffer around countries (km)
    Returns:
        None, but saves GeoJSON
    """
    # conversion https://www.usna.edu/Users/oceano/pguth/md_help/html/approx_equivalents.htm
    # 1 degree = 111 km
    # 1 km = 0.009 degrees
    buffer_country_deg = buffer_country * 0.009
    buffer_harbor_deg = buffer_harbor * 0.009
    # Read in the data
    harbors = gpd.read_file(
        "data" + os.sep + "geospatial_information" + os.sep + file_harbors
    )
    countries = gpd.read_file(
        "data"
        + os.sep
        + "geospatial_information"
        + os.sep
        + "Countries"
        + os.sep
        + file_countries
    )
    # Dissolve it
    countries_dissolved = gpd.GeoDataFrame(countries.dissolve()["geometry"])
    harbors_dissolved = gpd.GeoDataFrame(harbors.dissolve()["geometry"])
    # Create the buffers
    buffered_harbors = gpd.GeoDataFrame(harbors_dissolved.buffer(buffer_harbor_deg))
    buffered_harbors.columns = ["geometry"]
    buffered_countries = gpd.GeoDataFrame(
        countries_dissolved.buffer(buffer_country_deg)
    )
    buffered_countries.columns = ["geometry"]
    # Combine the two bufferes
    buffer_both = buffered_countries.union(buffered_harbors)
    # Substract the countries from the buffer
    buffer_diff = gpd.GeoDataFrame(buffer_both.difference(countries_dissolved))
    buffer_diff.columns = ["geometry"]
    buffer_diff = buffer_diff.dissolve()
    buffer_diff.to_file(
        "data"
        + os.sep
        + "interim_results"
        + os.sep
        + "harbor_{}km_coast_{}km_buffer.geojson".format(buffer_harbor, buffer_country),
        driver="GeoJSON",
    )
