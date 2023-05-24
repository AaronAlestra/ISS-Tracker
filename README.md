# ISS-Tracker
Tracks ISS in real time using Matlab App Designer

%--NOTE: Code between lines --1 and --2 is used to track ISS. All other code is premade through Matlab App Designer


classdef MatLabISSFeedAppCopy_exported < matlab.ui.componentcontainer.ComponentContainer

    % Properties that correspond to underlying components
    properties (Access = private, Transient, NonCopyable)
        Label             matlab.ui.control.Label
        ISSLiveFeedLabel  matlab.ui.control.Label
        Image             matlab.ui.control.Image
    end

  


    methods (Access = protected)
        
        % Code that executes when the value of a public property is changed
        function update(comp)
%---------------------------------------------------------------------------------------------1

% Function to retrieve and update ISS coordinates
function getISSCoordinates()
    % Send GET request to ISS API
    url = 'https://api.wheretheiss.at/v1/satellites/25544';
    options = weboptions('Timeout', 10);
    issData = webread(url, options);
    
    % Variables for ISS coordinates
    lat = num2str(issData.latitude);
    long = num2str(issData.longitude);
    velo = num2str(issData.velocity);
    visib = num2str(issData.visibility);
    alti = num2str(issData.altitude);
    latitude = [issData.latitude];
    longitude = [issData.longitude];
    
    
    % Output ISS coordinates to TextArea
   
    comp.Label.Text = ['Latitude:' lat 'º' newline 'Longitude:' long 'º' newline 'Velocity:' velo 'km/h' newline 'Altitude:' alti 'km' newline 'Visibility:' visib ];

 % Map with ISS Coordinates
 gx = geoaxes('Basemap','topographic','ZoomLevel',5,'axisColor','none');
 %gx = geoaxes('Basemap','satellite','ZoomLevel',6,'axisColor','none');
 % gx = geoaxes('Basemap','satellite','ZoomLevel',2);
 
 hold off
    
 geoplot(latitude,longitude,'.','MarkerSize',12,'Color','red');


end
        
% Set up timer to retrieve and update ISS coordinates every 2 seconds
t = timer('ExecutionMode', 'fixedRate', 'Period',2, 'TimerFcn', @(~,~) getISSCoordinates());
start(t);
 
 end
 
 %-----------------------------------------------------------------------------------------------------------2

        % Create the underlying components
        function setup(comp)

            % Get the file path for locating images
            pathToMLAPP = fileparts(mfilename('fullpath'));

            comp.Position = [1 1 1123 634];
            comp.BackgroundColor = [1 1 1];

            % Create Image
            comp.Image = uiimage(comp);
            comp.Image.Position = [2 1 1122 634];
            comp.Image.ImageSource = fullfile(pathToMLAPP, 'AppWallpaper.jpg');

            % Create ISSLiveFeedLabel
            comp.ISSLiveFeedLabel = uilabel(comp);
            comp.ISSLiveFeedLabel.FontName = 'Times New Roman';
            comp.ISSLiveFeedLabel.FontSize = 36;
            comp.ISSLiveFeedLabel.FontColor = [0.8549 0.9412 0.9412];
            comp.ISSLiveFeedLabel.Position = [458 566 212 48];
            comp.ISSLiveFeedLabel.Text = 'ISS Live Feed';

            % Create Label
            comp.Label = uilabel(comp);
            comp.Label.HorizontalAlignment = 'center';
            comp.Label.FontName = 'Times New Roman';
            comp.Label.FontSize = 36;
            comp.Label.FontColor = [0.8549 0.9412 0.9412];
            comp.Label.Position = [3 22 1121 545];
            comp.Label.Text = '';
        end
    end
end
